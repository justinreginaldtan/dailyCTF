# Day24 - imap_pop3_lab

- **Platform**: HackTheBox  
- **Category**: Email / Networking  
- **Difficulty**: Easy  
- **Date**: 2025-05-21  
- **Tools Used**: Nmap, OpenSSL, Bash  

---

## Summary
This lab focused on enumerating IMAP and POP3 services. By scanning with Nmap and connecting with OpenSSL, I authenticated with known credentials and accessed mailboxes. Reading through the inboxes revealed an admin email containing the flag.

---

## Enumeration
- Scanned target for IMAP/POP3 services:
  ```bash
  sudo nmap 10.129.14.128 -sC -sV -p 110,143,993,995
  ```
  - Discovered active services:
    - Port 110 → POP3  
    - Port 143 → IMAP  
    - Port 993 → IMAPS  
    - Port 995 → POP3S  

- Service banners revealed additional metadata such as service version and organization info.  
- Reused valid credentials from a previous lab: `robin:robin`.  

---

## Exploitation
- Connected to IMAPS with OpenSSL:
  ```bash
  openssl s_client -connect 10.129.14.128:imaps
  ```
- Logged in using standard IMAP commands with `robin:robin`.  
- Enumerated mailbox folders and read through messages.  
- Found an email addressed to an admin.  
- The message body contained the flag.  

---

## Lessons Learned
- IMAP and POP3 often reveal sensitive information when weak or reused credentials exist.  
- Combining enumeration across related services (SMTP + IMAP/POP3) builds a complete picture of the mail infrastructure.  
- OpenSSL is a versatile tool for manually testing encrypted mail services and learning IMAP/POP3 command syntax.  
- Misconfigured or weakly secured mailboxes are common entry points in CTF and real-world environments.  
