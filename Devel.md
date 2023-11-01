![](assets/Pasted%20image%2020230921115235.png)
# Summary
#### Findings:
*  Multiple kernel exploits: MS10_015,MS16_075, and MS15_051.
	* The system is running Windows 7, which has reached EOL and is no longer supported by Microsoft.
* Anonymous login to FTP server.
	* Anonymous login lets anyone login to the FTP server. The FTP server is located in the directory for the web server on port 80. This allows anyone to upload any file to the system, as well as execute server side scripts.
#### Recommendations:
* Multiple kernel exploits: MS10_015,MS16_075, and MS15_051.
	* Upgrade to either Windows 10 (which will reach end of servicing on October 14, 2025) or Windows 11.
* Anonymous login to FTP server.
	* Disable anonymous login.
# Attack Narrative
### Enumeration
* An Nmap scan was run on the provided IP address.  
![](assets/Pasted%20image%2020230921115336.png)
* As seen in the Nmap scan, the FTP service allows anonymous login.

* When logging into the FTP server as anonymous, there is complete access to a web server directory.  
![](assets/Pasted%20image%2020230921120131.png)
### Exploited vulnerabilities

* Uploading a test file to the FTP server shows that we have write access to the directory of the IIS web server on port 80.  
![](assets/Pasted%20image%2020230921121026.png)
* A malicious .aspx file was created through Msvenom, and uploaded via the FTP server.  
![](assets/Pasted%20image%2020230921122134.png)
* The file was accessed by navigating to http://10.129.110.19/shell.aspx. A connection was received from the server, through the multi/handler listener on Metasploit, providing access to the machine.
![](assets/Pasted%20image%2020230921121846.png)
* Metasploit's local_exploit_suggester shows that the system could be vulnerable to various kernel exploits.  
![](assets/Pasted%20image%2020230921132342.png)
* System vulnerable to MS10_015.  
![](assets/Pasted%20image%2020230921131112.png)
* System vulnerable to MS16_075.
![](assets/Pasted%20image%2020230921131603.png)
* System is vulnerable to MS15_051.
![](assets/Pasted%20image%2020230921131819.png)
