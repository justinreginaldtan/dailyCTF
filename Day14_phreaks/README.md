# Day14 - phreaks

- **Platform**: HackTheBox  
- **Category**: Forensics  
- **Difficulty**: Easy  
- **Date**: 2025-05-14  
- **Tools Used**: Wireshark, Bash, Base64  

---

## Summary
The challenge provided a `.pcap` file capturing SMTP traffic. By analyzing email packets, I identified a `.zip` attachment split across multiple fragments. After extracting and decoding the Base64 content and unlocking the archive with the included password, I recovered the final PDF which contained the flag.

---

## Enumeration
- Opened the capture file in Wireshark.  
- Observed SMTP traffic with `.zip` file attachments.  
- Applied filter:
  ```
  smtp.data.fragment
  ```
  which revealed 15 packets containing fragments of a `.zip` file.  
- Noted that each fragment contained:  
  - Base64-encoded blocks of the archive.  
  - A password included in the packets.  

---

## Exploitation
- Decoded each Base64 fragment and appended output to reconstruct the `.zip` file:
  ```bash
  echo "<base64 string>" | base64 -d >> challenge.zip
  ```
- Extracted files from the archive using the recovered password:
  ```bash
  unzip -P <password> challenge.zip
  ```
- Combined all extracted parts into a single PDF.  
- Opened the PDF and located the flag at the end of the document.  

---

## Lessons Learned
- SMTP traffic often contains encoded attachments that can be reconstructed directly from packet captures.  
- Base64 encoding is widely used in email transfers and should be a first thing to check.  
- Automation (e.g., loops or using `tshark -z follow` or Wireshark’s **Export Objects**) can save significant time when reassembling fragmented files.  
- Forensics challenges often reward careful observation and reassembly over brute force.  
