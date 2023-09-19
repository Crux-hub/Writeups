![](assets/Pasted%20image%2020230919141345.png)
# Summary
#### Findings:



#### Recommendations:


# Attack Narrative
### Enumeration
* An nmap scan revealed open ports on 80,445, and 8808.
* ![](assets/Pasted%20image%2020230919141407.png)

### Foothold
* I connected to the web server on port 80 using http://10.129.107.231/, and was redirected to a login page at http://10.129.107.231/login.php.
* I tried logging in with common user names and password combinations, such as admin and password. When username was invalid, I received a response saying that there was no account with that username. This leaves the application open to user enumeration and password brute forcing.
* ![](assets/Pasted%20image%2020230919141421.png)
* When signing up for an account I was able to find a SQLI vulnerability that allowed me to see other users notes that were stored on the web page. I signed up with an account and used the username ' or '1'='1
* ![](assets/Pasted%20image%2020230919141430.png)
* Since the username allowed any characters and was not sanitized, when I logged in with my new account, I was able to see other users notes.
![](assets/Pasted%20image%2020230919141441.png)
* In these notes was a username and password for an smb share named new-site. I was able to login with read and write capabilities to this smb share.
* ![](assets/Pasted%20image%2020230919141449.png)
* I was able to verify that this smb share was the directory for the web server on port 8808 by uploading a file and accessing it through http://10.129.107.231:8808/test.txt.
* ![](assets/Pasted%20image%2020230919141500.png)
* I used php code that allows for code execution if accessed. 
* ![](assets/Pasted%20image%2020230919141510.png)
* I then uploaded this php file as well as a netcat binary to the smb server.
* ![](assets/Pasted%20image%2020230919141520.png)
* I started the nc binary via the php code execution and was able to land a shell as tyler.
* ![](assets/Pasted%20image%2020230919141527.png)
* ![](assets/Pasted%20image%2020230919141533.png)
### Privilege Escalation
* In the file system there is a bash.lnk file, indicating possible Windows Subsystem for Linux installation. I searched for the bash.exe file, found and executed it.
* ![](assets/Pasted%20image%2020230919141541.png)
* I upgraded the shell to a pty shell, and check the history, finding admin smb credentials.
* ![](assets/Pasted%20image%2020230919141547.png)
* I used those credentials with psexec and was able to gain full access to the system.
* ![](assets/Pasted%20image%2020230919141556.png)