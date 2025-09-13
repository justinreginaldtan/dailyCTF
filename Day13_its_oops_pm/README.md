# Day13 - its_oops_pm

- **Platform**: HackTheBox  
- **Category**: Hardware  
- **Difficulty**: Easy  
- **Date**: 2025-05-13  
- **Tools Used**: Netcat, VHDL analysis  

---

## Summary
The challenge provided a Docker container that accepted 16-bit binary input over TCP and returned 16-bit output. By reviewing the provided VHDL files, I found a hardcoded pattern in `backdoor.vhdl`. Sending this exact binary string to the service triggered the backdoor and returned the flag.

---

## Enumeration
- Connected to the container via `nc`.  
- Examined provided files: multiple `.vhdl` files and a schematic image.  
- Discovered suspicious logic in `backdoor.vhdl`:
  ```vhdl
  constant pattern : STD_LOGIC_VECTOR(15 downto 0) := "1111111111101001";
  ```
- Recognized this pattern was likely the key input for triggering the backdoor condition.  

---

## Exploitation
- Connected with:
  ```bash
  nc <target-ip> <target-port>
  ```
- Submitted the 16-bit binary sequence:
  ```
  1111111111101001
  ```
- Service returned the flag immediately.  

---

## Lessons Learned
- Reading through provided source code often reveals intentional backdoors or weak points in hardware challenges.  
- Spotting hardcoded constants in logic files is a quick way to identify hidden triggers.  
- Reconnaissance within provided artifacts can drastically reduce guesswork, even in hardware/logic design CTFs.  
