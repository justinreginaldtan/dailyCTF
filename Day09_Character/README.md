# Day09 - character

- **Platform**: HackTheBox  
- **Category**: Scripting  
- **Difficulty**: Easy  
- **Date**: 2025-05-09  
- **Tools Used**: Bash, Grep, Netcat  

---

## Summary
The challenge presented a TCP service that returned individual characters of the flag when provided with an index. By determining the flag length and scripting requests with Bash and Netcat, I automated the extraction of all characters and reconstructed the full flag.

---

## Enumeration
- Connected to the challenge with:
  ```bash
  nc -n -v 94.237.63.198 42424
  ```
- Service prompt:
  ```
  Which character (index) of the flag do you want? Enter an index
  ```
- Tested multiple indexes manually to confirm behavior.  
- Determined the flag length (~130 characters) by probing different index values.  

---

## Exploitation
- Automated character extraction with:
  ```bash
  seq 0 130 | nc -n -v 94.237.63.198 42424 | tee character.log
  ```
- Processed the output to isolate characters and rebuild the flag:
  ```bash
  grep Index character.log | sed -E -e 's/^Which.*Index [0-9]+: (.+)/\1/' | tr -d '\n'
  ```
- Successfully reconstructed the full flag.  

---

## Lessons Learned
- Simple automation can drastically reduce time compared to manual input.  
- `grep` and `sed` are powerful for parsing structured output.  
- Always test for boundary conditions (e.g., maximum index) to determine flag length before automation.  
