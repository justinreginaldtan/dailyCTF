# Day23 - shush_protocol

- **Platform**: HackTheBox  
- **Category**: ICS  
- **Difficulty**: Easy  
- **Date**: 2025-05-23  
- **Tools Used**: Wireshark  

---

## Summary
Analyzed a PCAP capture of ICS traffic to extract plaintext credentials used by a control system when authenticating to a PLC. The challenge demonstrated how insecure or custom protocols often transmit sensitive data without encryption.

---

## Enumeration
- Loaded the `.pcapng` file in Wireshark.  
- Filtered down to TCP/UDP traffic to focus on meaningful host communications.  
- Identified active IP pairs representing PLC ↔ control system exchanges.  
- Sorted packets by length to highlight anomalous traffic.  
- Flagged a suspicious packet using an “Unknown Function” with a longer payload than others.

---

## Exploitation
- Followed the stream for the suspicious packet in Wireshark.  
- Inspected hex/ASCII payload and found a human-readable password string embedded in plaintext.  
- Extracted the password, completing the challenge.

---

## Lessons Learned
- Legacy ICS protocols often transmit sensitive credentials in plaintext, making packet captures highly revealing.  
- Sorting traffic by payload length is an effective triage method for spotting anomalous or diagnostic data.  
- Following suspicious streams in Wireshark can quickly surface exposed credentials in insecure environments.  
