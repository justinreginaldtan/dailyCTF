# Day15 - locked_away

- **Platform**: HackTheBox  
- **Category**: Pwn / Misc  
- **Difficulty**: Easy  
- **Date**: 2025-05-15  
- **Tools Used**: Python  

---

## Summary
The challenge simulated a restricted Python REPL protected by a blacklist. By clearing the blacklist at runtime, I was able to bypass the restrictions and call the hidden `open_chest()` function to retrieve the flag.

---

## Enumeration
- Connected to the remote Python REPL and reviewed the provided source code.  
- Observed that the function `open_chest()` returned the flag.  
- A blacklist was implemented to exit the program if forbidden keywords (e.g., `exec`, `eval`, `open`) were used.  
- Considered bypassing the blacklist by dynamically constructing words (`chr()`, `join`, etc.), but looked for simpler alternatives.  

---

## Exploitation
- Cleared the blacklist with:
  ```python
  blacklist.clear()
  ```
- Confirmed that restricted keywords were now usable.  
- Executed:
  ```python
  open_chest()
  ```
- Successfully retrieved the flag.  

---

## Lessons Learned
- Blacklists are weak controls; if not enforced properly, they can often be bypassed or removed.  
- Understanding the runtime environment and available built-in functions is key to exploiting sandboxed interpreters.  
- Enumeration of source code is critical — spotting the presence of `open_chest()` provided a direct target.  
