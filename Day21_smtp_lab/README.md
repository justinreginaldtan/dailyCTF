# Day21 - smtp_lab

- **Platform**: HackTheBox  
- **Category**: Mail / SMTP  
- **Difficulty**: Easy  
- **Date**: 2025-05-21  
- **Tools Used**: Nmap, smtp-user-enum, Kali Linux  

---

## Summary
The challenge involved enumerating an SMTP service on port 25. By scanning the service with Nmap, testing for open relay, and then using `smtp-user-enum` with a wordlist, I successfully identified valid users on the mail server.

---

## Enumeration
- Ran Nmap scan against port 25:
  ```bash
  sudo nmap 10.129.14.128 -sC -sV -p25
  ```
  - Output:
    ```
    InFreight ESMTP v2.11
    ```
- Verified version and confirmed target was running an SMTP server.  

---

## Exploitation
- Checked for open relay functionality with:
  ```bash
  sudo nmap 10.129.14.128 -p25 --script smtp-open-relay -v
  ```
  - Found the server did not allow open relay.  

- Used `smtp-user-enum` with a provided wordlist to enumerate users:  
  ```bash
  smtp-user-enum -M VRFY -U wordlist.txt -t 10.129.14.128
  ```
  - Successfully identified valid users on the server.  

---

## Lessons Learned
- SMTP enumeration often begins with scanning for open relay but typically requires user enumeration to gain meaningful results.  
- Tools like `smtp-user-enum` automate VRFY/EXPN/RCPT checks to quickly validate accounts.  
- Revisiting mail flow (MUA → MSA → MTA → MDA → MUA) helps contextualize where attackers can probe services.  
- Even when open relay is disabled, misconfigurations can leak valid usernames.  
