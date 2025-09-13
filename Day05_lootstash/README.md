# Day05 - lootstash

- **Platform**: HackTheBox  
- **Category**: Reverse Engineering  
- **Difficulty**: Easy  
- **Date**: 2025-05-05  
- **Tools Used**: file, strings, Ghidra/Cutter  

---

## Summary
The challenge provided a binary named `stash` which contained a large list of weapon names. By inspecting the binary with `strings` and later confirming in Ghidra, the flag was discovered directly in the `.rodata` section without requiring further exploitation.

---

## Enumeration
- Challenge file: `stash` (64-bit Linux ELF).  
- Confirmed type with:
  ```bash
  file stash
  # ELF 64-bit LSB executable, x86-64
  ```
- Extracted strings with:
  ```bash
  strings -n 6 stash | head -n 20
  ```
- Output included weapon names and the flag string:
  ```
  Bronze_Dagger
  Iron_Dagger
  Steel_Dagger
  ...
  Obsidian_Warhammer
  HTB{redacted}
  Mythic_Scythe
  ```

---

## Exploitation
- Opened the binary in Ghidra/Cutter.  
- Verified `.rodata` contained the weapon list and the flag in plaintext.  
- No dynamic exploitation needed — flag was simply embedded in the binary data section.  

---

## Lessons Learned
- Always run `strings` on a binary first — sometimes the flag is hidden in plain sight.  
- Disassemblers like Ghidra confirm the context of discovered strings, ensuring the flag is not a false positive.  
- Quick static analysis can often solve reverse engineering challenges faster than overcomplicating with runtime debugging.  
