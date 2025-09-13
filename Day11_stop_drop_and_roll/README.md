# Day11 - stop_drop_and_roll

- **Platform**: HackTheBox  
- **Category**: Scripting  
- **Difficulty**: Easy  
- **Date**: 2025-05-11  
- **Tools Used**: Python, Bash  

---

## Summary
The challenge presented a TCP service that sent words such as `GORGE`, `PHREAK`, and `FIRE`, which needed to be translated into `STOP`, `DROP`, and `ROLL`. I automated the process using a Python script with the `pwntools` library, allowing me to quickly handle the repeated prompts and capture the flag.

---

## Enumeration
- Connected to the service at `83.136.252.123:44244`.  
- Observed prompt/response behavior:  
  - `GORGE → STOP`  
  - `PHREAK → DROP`  
  - `FIRE → ROLL`  
- Confirmed the challenge required repeated input/translation until flag was revealed.  

---

## Exploitation
- Automated solution with a Python script:
  ```python
  from pwn import *

  r = remote('83.136.252.123', 44244)

  r.recvuntil(b'(y/n) ')
  r.sendline(b'y')
  r.recvuntil(b'\n')

  tries = 0

  while True:
      try:
          got = r.recvline().decode()

          payload = (
              got
              .replace(", ", "-")
              .replace("GORGE", "STOP")
              .replace("PHREAK", "DROP")
              .replace("FIRE", "ROLL")
              .strip()
          )

          r.sendlineafter(b'What do you do?', payload.encode())

          tries += 1
          log.info(f'{tries}: {payload}')

      except EOFError:
          log.success(got.strip())  # final line = flag
          r.close()
          break
  ```
- Script successfully looped through translations until the flag was revealed.  

---

## Lessons Learned
- Automating repetitive tasks with Python scripts saves significant time compared to manual input.  
- The `pwntools` library is well-suited for building interactive solutions against CTF-style services.  
- Even simple translation-based challenges highlight the power of scripting for efficiency and reliability.  
