# Day10 - guild

- **Platform**: HackTheBox  
- **Category**: Web  
- **Difficulty**: Easy  
- **Date**: 2025-05-10  
- **Tools Used**: WhatWeb, BurpSuite, Bash  

---

## Summary
The challenge involved a web application that allowed user-generated content uploads. By testing for Server-Side Template Injection (SSTI), I was able to leak admin details. Further investigation revealed a weak password reset mechanism that relied on predictable SHA-256 hashes, which allowed me to reset the admin password and retrieve the flag.

---

## Enumeration
- Target: `94.237.55.43:32902`.  
- Ran `whatweb` for initial reconnaissance.  
- Used BurpSuite to build a site map and analyze authentication flows.  
- Investigated file upload functionality for possible reverse shell exploitation.  
- Tested cookie handling for insecure role validation.  
- Discovered functionality where users could write/upload text for sharable links.  

---

## Exploitation
- Tested for SSTI with:
  ```
  {{1-1}}
  ```
  Confirmed the injection worked.  
- Exploited SSTI to enumerate and leak the admin’s username and email.  
- Found password reset relied on predictable SHA-256 hash of the email address.  
- Replicated reset link locally, allowing admin password reset.  
- Logged in as admin and accessed uploads.  
- Uploaded payload to retrieve the flag.  

---

## Lessons Learned
- Always test user-generated content for SSTI vulnerabilities.  
- Weak or predictable reset token generation makes password recovery flows insecure.  
- Upload features are common entry points and should be carefully validated.  
- Combining multiple vulnerabilities (SSTI + weak password reset) often leads to full compromise.  
