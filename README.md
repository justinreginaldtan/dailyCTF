# 🧠 dailyCTF — My Journey to Red Teaming

Hi, I’m **Justin Reginald Tan** — a senior CIS student passionate about red teaming and web application security.  

`dailyCTF` started in **May 2025** as a personal challenge: solve **one CTF or lab every day for a month** and document the journey. What began as a consistency experiment has grown into a full collection of **31 write-ups**, covering everything from beginner web exploits to multi-stage privilege escalation chains.  

This repo is both a technical logbook and a personal archive of growth — a place to show my process, share mistakes, and highlight lessons learned.  

---

## 🔍 Why I’m Doing This

CTFs and hands-on labs aren’t just puzzles — they are **reps for real-world offensive security**. Through this series I practiced:  

- Web exploitation (XSS, SQLi, file upload, XXE, SSTI, command injection)  
- Network services enumeration (SMTP, SNMP, IMAP/POP3, NFS, DNS)  
- Reverse engineering and pwn challenges  
- Linux privilege escalation and misconfigurations  
- Chaining smaller vulnerabilities into full compromises  

I also wanted to prepare for certifications like **OSCP / CPTS** while building the discipline of daily problem-solving.  

---

## 🧩 What You’ll Find Here

Each daily folder (Day01 → Day31) contains a **Markdown write-up** with:  

- ✅ Challenge overview and scenario  
- 🔍 Reconnaissance and methodology  
- 💥 Exploitation steps (commands, payloads, screenshots where needed)  
- 🧠 Lessons learned and key takeaways  

Not every day was a strict CTF — some entries are **HackTheBox Academy skill assessments** or training modules that matched the same learning goals.  

---

## 📅 Completed Challenges (May 2025)

```plaintext
Day01  Footprinting Hard         → DNS, SNMP, IMAP/POP3 chain  
Day02  Timekorp                  → PHP command injection  
Day03  Dynastic                  → Classical cipher (Trithemius)  
Day04  Flagcasino                → ELF reversing (partial)  
Day05  Lootstash                 → Strings & binary analysis  
Day06  Jailbreak                 → XXE exploitation  
Day07  Labyrinth Linguist        → SSTI in Apache Velocity  
Day08  An Usual Sighting         → SSH logs & forensic analysis  
Day09  Character                 → Flag extraction via scripting  
Day10  Guild                     → SSTI + IDOR to admin takeover  
Day11  Stop Drop and Roll        → Automated socket scripting  
Day12  Critical Flight           → PCB/Gerber forensics  
Day13  It’s Oops PM              → VHDL hardware backdoor  
Day14  Phreaks                   → SMTP PCAP → zip carving  
Day15  Locked Away               → Python blacklist bypass  
Day16  Hidden Path               → Hidden character manipulation  
Day17  GetSimpleCMS Box          → Web RCE + privilege escalation  
Day18  Void                      → ret2dlresolve ROP exploit  
Day19  Insecure NFS              → NFS share mount & flag retrieval  
Day20  DNS Footprinting          → Zone transfers & DNS enumeration  
Day21  SMTP Lab                  → SMTP user enumeration  
Day22  SNMP Enum                 → Community string brute-force  
Day23  Shush Protocol            → ICS packet capture analysis  
Day24  IMAP/POP3 Lab             → Mail service misconfig  
Day25  Initial Foothold Hard     → Nibbleblog exploit → foothold  
Day26  Web Proxy                 → Disabled button bypass & cookies  
Day27  XSS Cookie Theft          → Blind XSS → session hijack  
Day28  File Upload Attacks       → XXE + MIME bypass → RCE  
Day29  Web Attacks               → Verb tampering + IDOR + XXE  
Day30  Command Injection         → Filter bypass & obfuscation  
Day31  Linux Local Priv Esc      → User → Barry → Tomcat → Root chain
```

---

## 👋 Connect With Me

- 💼 [LinkedIn](https://linkedin.com/in/justinreginaldtan)  
- 💌 Feel free to open an issue or reach out — always open to learning together.  

---

Thanks for checking out `dailyCTF`.  