# Day19 - insecure_nfs

- **Platform**: HackTheBox  
- **Category**: Networking / Forensics  
- **Difficulty**: Easy  
- **Date**: 2025-05-19  
- **Tools Used**: Nmap, showmount, mount, dig  

---

## Summary
The challenge exposed an insecure NFS export named `nfsshare`. By enumerating exports, mounting the share with `nolock`, and reviewing its contents, I was able to access `flag.txt` and retrieve the flag.

---

## Enumeration
- Scanned the target with Nmap:
  ```bash
  nmap -sV -p 111,2049 <target-ip>
  ```
  - Confirmed NFS service was running.  

- Listed available exports:
  ```bash
  showmount -e <target-ip>
  ```
  - Found export: `/nfsshare`.  

- Mounted the share:
  ```bash
  sudo mount -t nfs -o nolock <target-ip>:/nfsshare /mnt
  ```

---

## Exploitation
- Navigated to the mounted share:
  ```bash
  ls -al /mnt
  ```
- Located `flag.txt`.  
- Read the file to retrieve the flag:
  ```bash
  cat /mnt/flag.txt
  ```

---

## Lessons Learned
- Insecure NFS exports often expose sensitive files to unauthenticated users.  
- Always check for world-readable shares when enumerating NFS.  
- The `nolock` option bypasses lock daemons, useful in CTF labs.  
- Proper configuration and access controls are critical to securing NFS services.  
