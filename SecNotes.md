![[Pasted image 20230919122252.png]]
# Summary
#### Findings:



#### Recommendations:


# Attack Narrative
### Enumeration
* An nmap scan revealed open ports on 80,445, and 8808.
![[Pasted image 20230919123320.png]]

### Foothold
* I connected to the web server on port 80 using http://10.129.107.231/, and was redirected to a login page at http://10.129.107.231/login.php.
* I tried logging in with common user names and password combinations, such as admin and password. When username was invalid, I received a response saying that there was no account with that username. This leaves the application open to user enumeration and password brute forcing.
![[Pasted image 20230919124843.png]]
* When signing up for an account I was able to find a SQLI vulnerability that allowed me to see other users notes that were stored on the web page. I signed up with an account and used the username ' or '1'='1
![[Pasted image 20230919125119.png]]
* Since the username allowed any characters and was not sanitized, when I logged in with my new account, I was able to see other users notes.
![[Pasted image 20230919125433.png]]
* In these notes was a username and password for an smb share named new-site. I was able to login with read and write capabilities to this smb share.
![[Pasted image 20230919130014.png]]
* I was able to verify that this smb share was the directory for the web server on port 8808 by uploading a file and accessing it through http://10.129.107.231:8808/test.txt.
  ![[Pasted image 20230919130537.png]]
  * I used php code that allows for code execution if accessed.
  ![[Pasted image 20230919131120.png]]
  * I then uploaded this php file as well as a netcat binary to the smb server.
  ![[Pasted image 20230919131418.png]]
  * I started the nc binary via the php code execution and was able to land a shell as tyler.
 ![[Pasted image 20230919131759.png]]
 ![[Pasted image 20230919131822.png]]
### Privilege Escalation
* In the file system there is a bash.lnk file, indicating possible Windows Subsystem for Linux installation. I searched for the bash.exe file, found and executed it.
![[Pasted image 20230919132900.png]]
* I upgraded the shell to a pty shell, and check the history, finding admin smb credentials.
![[Pasted image 20230919133115.png]]
* I used those credentials with psexec and was able to gain full access to the system.
![[Pasted image 20230919133721.png]]