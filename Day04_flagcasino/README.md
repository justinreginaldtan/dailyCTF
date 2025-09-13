# Day04 - flagcasino

- **Platform**: HackTheBox  
- **Category**: Reverse Engineering  
- **Difficulty**: Easy  
- **Date**: 2025-05-04  
- **Tools Used**: file, strings  

---

## Summary
The challenge provided a 64-bit ELF binary named `casino`. Initial recon identified welcome messages and response strings, suggesting it was an interactive binary where the goal was to reverse the logic and beat the game to retrieve the flag. This was a failed first attempt, but key reconnaissance steps were completed.

---

## Enumeration
- Challenge file: `casino` (64-bit ELF, not stripped).  
- Ran `file` command to confirm architecture:
  ```bash
  file casino
  # ELF 64-bit LSB pie executable, x86-64
  ```
- Ran `strings -n 6 casino` to extract readable strings:
  ```
  [ ** WELCOME TO ROBO CASINO ** ]
  [ * CORRECT * ]
  [ * INCORRECT * ]
  ```
- Identified it likely outputs “correct/incorrect” based on internal logic.

---

## Exploitation
- At this stage exploitation was not achieved.  
- Plan for next attempt:
  - Use `ltrace` or `strace` to trace library/system calls.  
  - Disassemble with `ghidra` or `radare2` to analyze the main function.  
  - Focus on logic controlling “CORRECT/INCORRECT” strings.  
  - Patch or input correct sequence to trigger the flag.  

---

## Lessons Learned
- Even without full exploitation, `file` and `strings` provide a quick baseline of a binary’s structure.  
- Capturing initial reconnaissance is useful for returning later with more advanced tools (disassemblers/debuggers).  
- Sometimes marking a challenge as “failed attempt” still builds momentum — the notes ensure no work is lost when revisiting.  
