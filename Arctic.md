![](assets/Pasted%20image%2020231002134125.png)

# Summary
#### Findings:
* CVE-2009-2265
	* Improper limitation of a pathname to a restricted directory, allows for directory traversal and remote code execution.
* SeImpersonate privilege available for the user tolis.
	* SeImpersonate  privileges allow for users to impersonate other accounts on the host.
* MS10-059
	* Windows server 2008 R2 is no longer supported by Microsoft and is vulnerable to kernel exploits.

#### Recommendations:
* CVE-2009-2265
	* Anything using FCKeditor before 2.6.4.1, should be updated.
* SeImpersonate privilege available for the user tolis.
	* SeImpersonate privilages almost always can lead to privilege escalation and should be disable on accounts.
* MS10-059
	* Host should be updated to a platform that is still supported by Microsoft such as Windows Server 2019 or Windows Server 2022.

# Attack Narrative
### Enumeration
* An Nmap scan was run on the provided IP address.  
![0](assets/Pasted%20image%2020231002121412.png)
* http://10.129.88.224:8500/CFIDE/administrator/enter.cfm shows that cold fusion 8 is being used.  
![](assets/Pasted%20image%2020231002131715.png)
### Exploited vulnerabilities
* The tester used the script at https://www.exploit-db.com/exploits/50057 to exploit. The lhost was modified to the testers IP address and the rhost was modified to the clients IP address. Then a netcat listener was setup on the Testers machine.  
![](assets/Pasted%20image%2020231002132210.png)
* The shell was Upgraded to a meterpreter shell by using the command generated my the Metasploit module exploit/multi/script/web_delivery.  
![](assets/Pasted%20image%2020231002132446.png)
* When users have SeImpersonate privilege, the host will be vulnerable to juicy potato attack.  
![](assets/Pasted%20image%2020231002130600.png)
* The Metasploit module ms16_075_reflection_juicy was used to escalate privileges to system.  
*![](assets/Pasted%20image%2020231002130755.png)
* Because the host is running on Microsoft server 2008, it is also vulnerable to the kernel exploit MS10-059.  The exploit file is provided at https://github.com/SecWiki/windows-kernel-exploits/blob/master/MS10-059/MS10-059.exe, then executed on the system.  
![](assets/Pasted%20image%2020231002135940.png)