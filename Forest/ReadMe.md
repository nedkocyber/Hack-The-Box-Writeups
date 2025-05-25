# Forest - Windows(Easy)
## Summary
Forest is an easy-level Windows Active Directory machine that demonstrates common misconfigurations in domain environments. Initial enumeration reveals several open services including LDAP and Kerberos. An anonymous LDAP bind allows user enumeration, where one of the accounts has Kerberos pre-authentication disabled. This opens the door to an AS-REP Roasting attack, from which the password hash is recovered and cracked. Using Evil-WinRM, access is gained as a domain user. With BloodHound, it's discovered that the user has privileges to perform a DCSync attack. By creating a new privileged user and leveraging secretsdump.py, the NTLM hash of the Domain Admin is obtained, allowing full system compromise.
## Nmap Enumeration
First we do a full port scan to see what are the services are open and working

![image](https://github.com/user-attachments/assets/005abea6-ac93-4db7-a95a-896693a137b4)

After we see all the open ports we are going to do a more detailed scan with the flags -sC -sV or -sCV for shor

![image](https://github.com/user-attachments/assets/4834df4c-5930-4343-82e5-30495de11e51)
## LDAP Enumeration
From the scan we see the domain name is htb.local after that we see port 389 is open and we try connecting to it
With the command :
nxc ldap 10.10.10.161 http://10.10.10.161/ -u "" -p "" --users | tee raw_ldap_output.txt
We can see what is avalible on that port 
Now with this command :
cat raw_ldap_output.txt | grep -oP 'CN=\K[^,]+' | sort -u > users.txt
We get all the users and put them in the users.txt text file

After we have all the users in 1 file we can use a script from the Impacket collection that helps with AS-REP Roasting
The Python script is named GetNPUsers.py

## AS-REP Roasting

![image](https://github.com/user-attachments/assets/a10f772d-46ee-4325-b724-d8e821479d6d)

From all the users in the test file only has pre-auth dissabled and thats svc-alfresco

![image](https://github.com/user-attachments/assets/a378b1f3-3628-4325-b510-7df29c82ebf4)


## Crack the Hash
![image](https://github.com/user-attachments/assets/2c2caea9-869f-40e6-b328-2246d0b78086)

We save the hash in a file and crack it with hashcat

![image](https://github.com/user-attachments/assets/89fde8bb-75a8-4a4d-8171-63c11ad39187)

After we cracked the hash we can use evil-winrm to connect to the machine

<img width="502" alt="image" src="https://github.com/user-attachments/assets/88e5e96a-62de-49ae-a8dc-37fefe060566" />

after logging we get the user flag and start our Privilege Escalation phase

## Privilege Escalation

From the screen shot above we see a bloodhound scan so we transfer it to our machine to run in in bloodhound and start looking throw the Active Directory

![image](https://github.com/user-attachments/assets/2c1b7131-79e3-4fd8-a14e-38f06ba2aa67)

its important when we have the GUI in bloodhound to amrk our user as pwned

![image](https://github.com/user-attachments/assets/ddda7ad5-dc5b-495c-b616-68c241db16c2)

Here we add a user named hacked that is a part of the group Domain Users and then add him to the other 2 important groups

![image](https://github.com/user-attachments/assets/a5e43f20-4acc-4bd4-8f19-b8abeef0bc69)
![image](https://github.com/user-attachments/assets/bc0e8273-1167-40c2-ad3c-b46de8cde78a)
![image](https://github.com/user-attachments/assets/b48887e8-5449-440e-b7f7-0c904d2a814a)

The newly added user sould look like this when added in all groups
![image](https://github.com/user-attachments/assets/eb5062bb-844b-4869-a919-a7c226085be3)

little disclamer: I did the Bypass-4MSI before i added the new user in all groups thats why i ahve only 6 options

Now we dissable Bypass-4MSI because it deactivates Microsoft Software Restriction Policies (SRP) it also bypasses  AMSI (Antimalware Scan Interface) overall when we do that there is nothing that can stop us from executing malicious PowerShell scripts

![image](https://github.com/user-attachments/assets/ef7004bb-af05-43cc-a881-3b6e254a1be7)

After all that we are going to use a powershell script called PowerView.ps1 that gives DcSync privileges to the user that we created

![image](https://github.com/user-attachments/assets/7837b8b7-d28a-48ae-8762-f1b7cac2e2f2)
Here we set up a simple python http server to send the file to the target

![image](https://github.com/user-attachments/assets/87306b94-2910-4865-b814-548ae9cfacf3)


Now looking at the bloodhount graph we see the acl Writedacl and we are going to exploit it

![image](https://github.com/user-attachments/assets/96dced91-5867-458a-8907-9731553fa520)

Following theese 4 simple comands (that costed 1 hour)

![image](https://github.com/user-attachments/assets/672ae813-13cb-4be4-ae42-7f4f2ec1f951)

We are able to dump all the hashes 

![image](https://github.com/user-attachments/assets/f1fcbd1a-3380-4480-9562-fbf832b20fc9)

Нow we are going to use psexec.py again from the Impacket Collection

This script helps with :
- lets you run commands or get an interactive shell on a remote Windows host
- It can execute commands remotely
- helps escalateing privileges to SYSTEM level if you have admin access

![image](https://github.com/user-attachments/assets/1bbb62be-741e-429b-b9c8-3937944aaf0c)

After we have a shell as nt authority/system we get the root flag

<img width="274" alt="image" src="https://github.com/user-attachments/assets/07395813-265d-41a0-9272-9a6bcae34f77" />






