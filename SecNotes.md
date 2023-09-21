![](assets/Pasted%20image%2020230919141345.png)
# Summary
#### Findings:
* SQL injection in the Username value of http://10.129.107.231/register.php.
	* Gives attackers the ability to see information from other users.
* Guessable usernames when logging into http://10.129.107.231/login.php
	* When trying to login with an account, it will tell the user if the username exists. This allows attackers to enumerate usernames, and possibly brute force passwords.
* Clear text passwords stored on web server and bash history.
	* Gives attackers access to accounts that they would otherwise not have access to.

#### Recommendations:
* SQL injection
	* All inputs from a user on the web server should be sanitized.
* Username enumeration
	* When attempting to login with an account that does not exist, generic error messages should be displayed. Such as "Invalid username or password."
* Clear text passwords
	* Passwords should not be stored in clear text, anyone who gains access to the file system will be able to see them.
* Passwords stored in bash history
	* This can be avoided by stopping history logging or sending history to /dev/null instead. More info here: https://attack.mitre.org/techniques/T1552/003/
# Attack Narrative
### Enumeration
* An nmap scan revealed open ports on 80,445, and 8808.  
   ![](assets/Pasted%20image%2020230919141407.png)

### Exploited vulnerabilities
* A connection to the web server was established on port 80 using http://10.129.107.231/, which was redirected to a login page at http://10.129.107.231/login.php.
* The tester attempted to log in with common user names and password combinations, such as 'admin' and 'password'. When logging in with an invalid username, a response was received stating that there was no account with that username. This leaves the application open to user enumeration and password brute forcing.  
  ![](assets/Pasted%20image%2020230919141421.png)
* When signing up for an account an SQLI vulnerability was exposed. An account was made for the username ' or '1'='1. Since the input for username is not sanitized before returning to the server, an attacker is able to manipulate SQL statements used in this parameter.  
   ![](assets/Pasted%20image%2020230919141430.png)
* Because of the SQLI, when signing in with this account all other users notes are visible. 
![](assets/Pasted%20image%2020230919141441.png)
* In these notes was a username and password for an SMB share named new-site. The tester was able to login with read and write capabilities to this SMB share.  
  ![](assets/Pasted%20image%2020230919141449.png)
* The tester was able to verify that this SMB share was the directory for the web server on port 8808 by uploading a file and accessing it through http://10.129.107.231:8808/test.txt.   
  ![](assets/Pasted%20image%2020230919141500.png)
* Because of the write access to the website directory, PHP code can be to be used to allow for code execution when accessed accessed.  
   ![](assets/Pasted%20image%2020230919141510.png)
* The PHP  file as well as a Netcat binary was uploaded to the website directory, using the smb server.   
![](assets/Pasted%20image%2020230919141520.png)
* The Netcat binary was started via the PHP code execution, which gave the tester a shell as the user tyler.  
![](assets/Pasted%20image%2020230919141527.png)  
![](assets/Pasted%20image%2020230919141533.png)
### Privilege Escalation
* In the file system there is a bash.lnk file, indicating possible Windows Subsystem for Linux installation. After searching for the bash.exe, the file was executed.  
![](assets/Pasted%20image%2020230919141541.png)
* The shell was upgraded to a pty shell, to provide more functionality. When checking the bash history, administrator credentials for the SMB share were revealed. 
![](assets/Pasted%20image%2020230919141547.png)
* The administrator credentials were used with psexec and the tester was able to gain full access to the system.  
![](assets/Pasted%20image%2020230919141556.png)