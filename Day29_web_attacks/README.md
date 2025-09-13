# Day29 - Web Attacks (HTB Skill Assessment)

- **Platform**: HackTheBox  
- **Category**: Web / Access Control / XXE  
- **Difficulty**: Medium  
- **Date**: 2025-05-29  
- **Tools Used**: BurpSuite, Intruder, Decoder  

---

## Summary
This assessment combined several vulnerabilities into a single exploitation chain. Starting from a normal user account, I escalated privileges by abusing **HTTP Verb Tampering** and an **IDOR**, then leveraged an **XXE injection** to read `/flag.php`.  

---

## Enumeration
- Target application required authentication.  
- Supplied credentials:  
  ```
  Username: htb-student
  Password: Academy_student!
  ```
- Application features:  
  - Reset Password form under Settings (did not require old password).  
  - `/api.php/user/{uid}` endpoint revealed user info based on UID.  

---

## Exploitation

### Step 1 — HTTP Verb Tampering
- The password reset endpoint (`/reset.php`) was designed for POST requests.  
- Changing the method to **GET** bypassed access controls, allowing arbitrary password resets.  

---

### Step 2 — IDOR Enumeration
- The `/api.php/user/{uid}` endpoint exposed user details based on the `uid` parameter.  
- Used Burp Intruder to fuzz values `1–100`.  
- Found an account with `"company": "Administrator"`.  

---

### Step 3 — Privilege Escalation
- Using HTTP Verb Tampering, reset the admin user’s password with a crafted GET request:  
  ```
  GET /reset.php?uid=5&token=<valid-token>&password=newpass
  ```
- Logged in as the admin with the new credentials.  
- Gained access to the **Add Event** feature, which processed XML input.  

---

### Step 4 — XXE Injection
- Event creation reflected XML payloads → confirmed XXE vulnerability.  
- Crafted malicious XML to read files from the server:  
  ```xml
  <!DOCTYPE foo [<!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=/flag.php"> ]>
  <event>
    <title>&xxe;</title>
  </event>
  ```
- The response returned the base64-encoded contents of `flag.php`.  
- Decoded in Burp Decoder → revealed the flag.  

- **Flag:** `HTB{REDACTED}`  

---

## Lessons Learned
- **HTTP Verb Tampering** can silently bypass security if developers only test one HTTP method.  
- **IDORs** remain common and dangerous when identifiers are sequential and unauthenticated.  
- **XXE** provides both file disclosure and system insight — always sanitize XML parsers.  
- Chaining low-to-medium vulnerabilities together often leads to **full compromise**.  
