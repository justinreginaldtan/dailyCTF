# Day03 - dynastic

- **Platform**: HackTheBox  
- **Category**: Cryptography  
- **Difficulty**: Easy  
- **Date**: 2025-05-03  
- **Tools Used**: Python  

---

## Summary
The challenge provided a custom Python script that encrypted text by shifting each letter forward by its index position, essentially implementing a Trithemius cipher. By reversing the operation, I decrypted the ciphertext in `output.txt` and recovered the flag.

---

## Enumeration
- Received two files:  
  - `source.py` (Python encryption logic)  
  - `output.txt` (encrypted string to be decrypted)  
- Examined `source.py` and found loop logic that shifted each alphabetic character forward by its index.  
- Recognized this pattern as a Trithemius cipher, a variant of a Caesar shift where the key increments with position.  

---

## Exploitation
- Wrote a Python script to reverse the encryption by subtracting the index value instead of adding it:
  ```python
  cipher = open("output.txt").read().strip()

  plain = []
  for i, ch in enumerate(cipher):
      if ch.isalpha():
          idx = (ord(ch) - 65 - i) % 26
          plain.append(chr(idx + 65))
      else:
          plain.append(ch)

  print("".join(plain))
  ```
- Running the script successfully decrypted the text and revealed the flag in the format `HTB{...}`.  

---

## Lessons Learned
- Always analyze provided source code first; even small indexing operations can reveal the full encryption scheme.  
- Incremental Caesar ciphers (like Trithemius) are straightforward to break when the algorithm is exposed.  
- Writing a quick decryption script is often faster and more reliable than manual analysis for position-based shifts.  
