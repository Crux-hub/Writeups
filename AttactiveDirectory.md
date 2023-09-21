![](https://i.imgur.com/Z8zgznd.png)
# Summary
#### Findings:
* Weak password policy
	* Svc-admin's password hash was able to be cracked relatively quickly with rockyou.txt, which is a very common wordlist used by attackers.
* Preauthentication enabled
	* When preauthentication is enabled, it allows attackers easy access to that accounts password hash.
* Clear text passwords
	* Passwords should not be stored in clear text, anyone who gains access to the file system will be able to see them. Although the password stored in the backup_credentials.txt file was encoded with Base64, most attackers will recognize that and be able to decode it immediately.

#### Recommendations:
* Weak password policy
	* Introduction of a strong password policy that ensures password length, complexity, reuse and aging.
* Preauthentication enabled
	* Disable preauthentication on accounts that don't require it.
	* On accounts that do require it, be sure to use a very strong password, that would be too hard to crack.
* Clear text passwords
	* Avoid storing passwords in files. Base64 encoding is easily decoded.

# Attack Narrative
### Enumeration
* An Nmap scan was run on the provided IP address.  
![](assets/Pasted%20image%2020230920182834.png)
* A list of possible usernames was provided by the client, which is stored in the file user.txt.
* With the information provided by the client, as well as the Nmap scan, a program called kerbrute was used to enumerate usernames from the network.  
  ![](assets/Pasted%20image%2020230920184316.png)
### Exploited vulnerabilities
* While enumerating the usernames from the network, kerbrute found that svc-admin's account does not require pre auth, which allows an attacker to access the hash for this users password. GetNPUsers outputs the hash in a format for hashcat to crack it.   
![](assets/Pasted%20image%2020230920185616.png)
* Hashcat was able to crack the hash quickly, indicating a weak password.  
![](assets/Pasted%20image%2020230920190204.png)  
![](assets/Pasted%20image%2020230920190302.png)
* The svc-admin account has read access to an SMB share called backup.  
![](assets/Pasted%20image%2020230920191207.png)
* The SMB share has a file in it called backup_credentials.txt.  
![](assets/Pasted%20image%2020230920191627.png)
* The file backup_credentials.txt contains the password for the backup account, encoded in base64. This is not recommended because anyone can decode this, without any sort of key.  
![](assets/Pasted%20image%2020230920191805.png)
* With the credentials for the backup account, the tester was able to access the NTDS.DIT file, exposing all of the password hashes for accounts, including Administrator.  
![](assets/Pasted%20image%2020230920193437.png)
* With the Administrators hash, the tester has full access to the domain controller.  
![](assets/Pasted%20image%2020230920193618.png)