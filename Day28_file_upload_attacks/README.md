# Day28 - File Upload Attacks (HTB Skill Assessment)

- **Platform**: HackTheBox  
- **Category**: Web / File Upload / RCE  
- **Difficulty**: Medium  
- **Date**: 2025-05-28  
- **Tools Used**: BurpSuite, PayloadsAllTheThings, PHP server  

---

## Summary
The assessment simulated a vulnerable e-commerce site in early development. The objective was to exploit the file upload form to gain **remote code execution (RCE)** and read a flag stored at the system root.

---

## Enumeration
- Targeted `/contact` endpoint with a **file upload form** in the “Contact Us” section.  
- Standard image uploads (`.jpg`) were accepted and stored under `user_feedback_submissions/`, with filenames renamed using a `YearMonthDay_` prefix.  
- Upload logic applied three checks:  
  1. Blacklist against `.php`, `.phtml`, etc.  
  2. Whitelist requiring `.jpg`, `.png`, etc.  
  3. MIME validation for `image/*g`.  

---

## Exploitation

### Filter bypass attempts
- Tested multiple extensions with Burp Intruder: `.jpg.php`, `.php5`, `.pht`, `.phar.jpg`.  
- `.phar.jpg` returned a different response size, suggesting partial bypass.  

### MIME type fuzzing
- Found that `image/svg+xml` was accepted.  
- Crafted an **XXE payload** inside an SVG file to exfiltrate:  
  - `/etc/passwd`  
  - `upload.php` source code via `php://filter/base64-encode`.  

### Analyzing upload.php
Revealed three layers of validation (extension, whitelist regex, and MIME check). From this, valid bypass extensions included `.phar.jpg` and `.phz.jpg`.

### Achieving RCE
- Crafted a PHP webshell disguised as an image by prepending a fake JPEG header.  
- Uploaded successfully as `250528_shell.phar.jpg`.  
- Accessed via:  
  ```
  /contact/user_feedback_submissions/250528_shell.phar.jpg?cmd=id
  ```
- Confirmed RCE with `id`, then used `ls` and `pwd` to enumerate directories.  
- Traversed up with `../../../..` to reach `/` and read the flag.  

- **Flag:** `HTB{REDACTED}`  

---

## Lessons Learned
- File upload vulnerabilities often combine **extension tricks** + **MIME type bypasses**.  
- **XXE payloads in SVG** can disclose sensitive files and application source.  
- Prepending file headers (e.g., JPEG signatures) is a simple but effective way to bypass MIME sniffing.  
- Upload directories should always be **non-executable** and isolated from webroot.  
- Chaining smaller misconfigurations (extension bypass → XXE → MIME bypass → traversal) can escalate to **full compromise**.  
