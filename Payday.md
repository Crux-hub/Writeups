# OffSec Proving Grounds: Payday
# Summary
#### Findings:
* Weak password policy
	* Multiple user accounts were using their username as a password, including Admin accounts.
* Outdated software
	* The web server on port 80 is running outdated CS-cart 1.3.3.
* Excessive permissions for non Admin accounts
	* User Patrick was able to use sudo for any command on the Linux system. Excessive user permissions can allow attackers to move both laterally and vertically.
#### Recommendations:
*  Weak password policy
	* Introduction of a strong password policy that ensures password length, complexity, reuse and aging.
* Outdated software
	* Update to most recent patch of software (4.16.2 at the time of this report). Implement policies to ensure timely patching of all software. Outdated software can lead to vulnerabilities that can be exploited by attackers.
* Excessive permissions for non Admin accounts
	* Implement the principal of least privilege for all accounts.
# Attack Narrative
### Enumeration
* An Nmap scan was run on the provided IP address.  
![](assets/Pasted%20image%2020231105130524.png)
![](assets/Pasted%20image%2020231105130613.png)
![](assets/Pasted%20image%2020231105130637.png)
* Port 80 is hosting a website.  
![](assets/Pasted%20image%2020231105130927.png)
* Running a directory scan, it shows there is a /admin directory that stands out.
![](assets/Pasted%20image%2020231105131122.png)
![](assets/Pasted%20image%2020231105131313.png)
* Navigating to the page shows a login portal.
![](assets/Pasted%20image%2020231105131350.png)
### Exploited vulnerabilities
* Attempted to login with common passwords (admin:password, admin:admin). Successfully logged in to admin page with admin:admin.
![](assets/Pasted%20image%2020231105131504.png)
* Under the upgrade center link on the administration panel, the cs cart version was found.  
![](assets/Pasted%20image%2020231105131645.png)  
* A quick google search shows that this version is vulnerable to remote code execution via a malicious phtml file upload. https://www.exploit-db.com/exploits/48891.
* Following the instructions on the exploit page, the tester navigated to template editor, and uploaded a malicious PHP file (with renamed file extention to .phtml, to bypass .php file upload restrictions). The malicious PHP file was found at https://github.com/pentestmonkey/php-reverse-shell.  
![](assets/Pasted%20image%2020231105132250.png)
* The tester then setup a Netcat listener and navigated to the path specified by the exploit. A shell was received from user www-data.
![](assets/Pasted%20image%2020231105132510.png)
![](assets/Pasted%20image%2020231105132556.png)
* When searching through the files on the web server, a config.php file was found.  
![](assets/Pasted%20image%2020231105132935.png)
* Reading the contents of the file shows a Msql user and password.  
![](assets/Pasted%20image%2020231105133049.png)
* Using the provided credentials, the tester accessed the Mysql database and browsed the tables.  
![](assets/Pasted%20image%2020231105133412.png)
![](assets/Pasted%20image%2020231105133348.png)
* Accessing the cscart_users table, there are two password hashes stored.  
![](assets/Pasted%20image%2020231105133923.png)  
![](assets/Pasted%20image%2020231105133833.png)  
* Running these passwords through Hashcat reveals a common theme of usernames as passwords.  
![](assets/Pasted%20image%2020231105134127.png)
![](assets/Pasted%20image%2020231105134109.png)
* Checking the /home folder, the user Patrick has a folder.  
![](assets/Pasted%20image%2020231105134218.png)  
* Port 22 is open on the machine, which is used for SSH connections. The tester attempted to login with patrick:patrick.  
![](assets/Pasted%20image%2020231105134410.png)  
* Host key type needs to be specified. Login success.  
![](assets/Pasted%20image%2020231105134555.png)  
* After checking sudo permissions for Patrick, it is revealed that Patrick can run any command as sudo.  
![](assets/Pasted%20image%2020231105134741.png)  
* Running sudo su gives the tester access to the root account.  
![](assets/Pasted%20image%2020231105134909.png)  