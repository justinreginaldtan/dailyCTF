# Day08 - an_usual_sighting

- **Platform**: HackTheBox  
- **Category**: Forensics  
- **Difficulty**: Easy  
- **Date**: 2025-05-08  
- **Tools Used**: Terminal, log analysis  

---

## Summary
This challenge involved analyzing SSH logs and a `.bash_history` file to identify an attacker’s activity. By correlating timestamps and events, I tracked the attacker’s login, extracted their key fingerprint, and identified their final executed command.

---

## Enumeration
- Provided artifacts: SSH logs and bash history.  
- Reviewed SSH logs for:  
  - First successful login  
  - Unusual login time  
  - Public key fingerprint of attacker  
- Cross-referenced suspicious log entries with `.bash_history` timestamps.  

---

## Exploitation
- Identified the attacker’s login event in SSH logs.  
- Extracted key details:  
  - Server IP and port  
  - First successful login time  
  - Public key fingerprint of attacker  
- Correlated these details with `.bash_history`.  
- Determined the attacker’s final executed command.  

---

## Lessons Learned
- SSH logs often reveal suspicious activity through failed attempts, unusual root logins, or odd-hour sessions.  
- `.bash_history` provides valuable correlation points to confirm attacker behavior.  
- Attackers frequently attempt cleanup (e.g., `shred` or removing history), but their traces can remain visible.  
- Even simple forensic analysis can piece together a timeline of intrusion events.  
