# Day26 - Web Proxy (HTB Skill Assessment)

- **Platform**: HackTheBox  
- **Category**: Web / Proxy / BurpSuite  
- **Difficulty**: Medium  
- **Date**: 2025-05-26  
- **Tools Used**: BurpSuite, Decoder, Intruder, Metasploit  

---

## Summary
This lab focused on web proxy manipulation using BurpSuite. I solved four tasks that tested response tampering, cookie decoding, hash fuzzing with Intruder, and manual verification of a Metasploit module request.

---

## Enumeration
- Discovered multiple web endpoints:  
  - `/lucky.php` with a disabled button.  
  - `/admin.php` using an encoded cookie for authentication.  
- Metasploit `coldfusion_locale_traversal` auxiliary module was also tested against a provided target with proxy interception.  

---

## Exploitation

### Q1: Disabled button bypass  
- Intercepted `/lucky.php` response in BurpSuite.  
- Modified the attribute from `disabled` → `enabled`.  
- Forwarded the modified response and clicked the button.  
- **Flag:** `HTB{d154bl3d_bu770n5_w0n7_570p_m3}`  

---

### Q2: Encoded cookie decoding  
- Captured cookie from `/admin.php`.  
- Observed layered encoding:
  1. ASCII Hex → Base64  
- Decoded twice to obtain a **31-character value**:  
  ```
  3dac93b8cd250aa8c1a36fffc79a17a
  ```  

---

### Q3: MD5 hash completion & Intruder fuzzing  
- Cookie value looked like an **incomplete MD5 hash** (missing final character).  
- Used Burp Intruder:  
  - Payload: `alphanum-case.txt` wordlist.  
  - Processing rules:  
    1. Prefix with known 31 characters.  
    2. Encode Base64 → then Hex (reverse of decoding).  
- Detected a response with a unique size.  
- Verified in Repeater to reveal flag.  
- **Flag:** `HTB{burp_1n7rud3r_n1nj4!}`  

---

### Q4: Metasploit request verification  
- Configured `auxiliary/scanner/http/coldfusion_locale_traversal` with Burp proxy.  
- Captured the request sent by Metasploit in BurpSuite.  
- Identified the directory referenced in:  
  ```
  /CFIDE/administrator/..
  ```  

---

## Lessons Learned
- **Response tampering** is a quick win — small modifications like flipping `disabled` → `enabled` can bypass UI restrictions.  
- **Layered encoding** often stacks simple schemes (Hex, Base64). Recognizing patterns (`==` for Base64) speeds decoding.  
- **Burp Intruder** is extremely powerful when combined with payload processing rules — chaining encoding steps was the key to fuzzing the final MD5 character.  
- **Metasploit + Burp proxy** is a strong combo for validating what modules actually send, helping transition from automated scans to manual verification.  
