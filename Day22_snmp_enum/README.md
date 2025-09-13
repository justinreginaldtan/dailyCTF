# Day22 - snmp_enum

- **Platform**: HackTheBox  
- **Category**: SNMP / Networking  
- **Difficulty**: Easy  
- **Date**: 2025-05-22  
- **Tools Used**: onesixtyone, braa  

---

## Summary
The challenge required brute-forcing SNMP community strings against a target host. Using `onesixtyone`, I discovered that the default `public` string was valid, and with `braa` I enumerated system information including a custom SNMP agent version.

---

## Enumeration
1. **Brute-force community string**
   ```bash
   onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp.txt 10.129.14.128
   ```
   - Found valid string: `public`.  
   - Output:
     ```
     10.129.14.128 [public] Linux htb 5.11.0-37-generic …
     ```

2. **Probe system group with braa**
   ```bash
   braa public@10.129.14.128:.1.3.6.*
   ```
   - Output revealed metadata:
     ```
     sysName     : htb
     sysLocation : InFreight SNMP v0.91
     ```

---

## Exploitation
- Using the discovered community string, I queried system information through OIDs.  
- Retrieved system name (`htb`) and confirmed the custom SNMP agent version (`InFreight SNMP v0.91`).  
- This information was sufficient to complete the lab objectives.  

---

## Lessons Learned
- SNMP enumeration often starts with discovering community strings (`public`, `private` are common defaults).  
- Tools like `onesixtyone` and `braa` make quick work of brute-forcing and querying SNMP data.  
- Understanding OIDs and MIBs is key to extracting valuable information from SNMP-enabled systems.  
- Even simple SNMP misconfigurations can leak system metadata useful for further attacks.  
