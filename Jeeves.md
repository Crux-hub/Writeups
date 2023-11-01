![](assets/Pasted%20image%2020230922215851.png)
# Summary
#### Findings:
* MS16_015
	* There is a vulnerability in the SMB server that can allow attackers to escalate to admin privileges.
* Jenkins server page at http://10.129.112:50000/askjeeves, no authentication required.
	* anyone connected to the network can access this server page and change the information without credentials. There is also access to run groovy scripts from the server.
* Improper error handling
	* Detailed error messages should never be revealed. Such details can provide attackers important clues on potential flaws in the site.
#### Recommendations:
* MS16_015
	* Can be fixed by updating to the latest security patches for Windows 10. more info: https://www.tenable.com/plugins/nessus/91603.
* Jenkins server page at http://10.129.112:50000/askjeeves, no authentication required.
	* Authentication with a username and strong password should be put in place, to view and edit this information.
* Improper error handling
	* When returning error messages to the user, they should be generic and not include any detailed information like file paths, software versions, etc. More info: https://owasp.org/www-community/Improper_Error_Handling
# Attack Narrative
### Enumeration
* An Nmap scan was run on the provided IP address. 
![](assets/Pasted%20image%2020230922215236.png)
* When accessing http://10.129.112, and searching for anything in the search bar, the tester found improper error handling.
![](assets/Pasted%20image%2020230922214701.png)
* Running a directory buster shows there is a URL at http://10.129.112:50000/askjeeves.
![](assets/Pasted%20image%2020230922143344.png)
* After navigating to the Manage Jenkins tab on the left, the tester had access to a script console.  
![](assets/Pasted%20image%2020230922144653.png)
### Exploited vulnerabilities
* The console allows execution of groovy scripts.
![](assets/Pasted%20image%2020230922143903.png)
* Using code from https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76, the tester was able to gain access to the system under the user kohsuke.  
![](assets/Pasted%20image%2020230922144314.png)
* Running metasploit exploit suggester, many possible vulnerabilities are shown.
![](assets/Pasted%20image%2020230922151420.png)
* The Windows host is vulnerable to MS16_075, giving the tester full access to the system.
![](assets/Pasted%20image%2020230922152417.png)
