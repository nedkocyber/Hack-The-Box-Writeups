# UnderPass -Linux(Easy)

## Summary
UnderPass is an easy-level Linux machine that emphasizes comprehensive enumeration and privilege escalation techniques. The challenge begins with identifying open TCP ports, leading to the discovery of an SNMP service. Through SNMP enumeration, a daloRADIUS web interface is uncovered, accessible via default credentials. Within this interface, a password hash for the user svcMosh is found and subsequently cracked, granting SSH access. Privilege escalation is achieved by exploiting the misconfigured mosh-server, which allows the user to execute it with root privileges, ultimately leading to a root shell.

## Nmap Enumeration
We start with a full port scan and after we see what ports are open we scan them for versions and default scripts

![image](https://github.com/user-attachments/assets/956216aa-7af6-43b9-8b95-b672a92411a6)

## Snmp Enumeration
After running snmp we see a couple of important data to us

<img width="485" alt="image" src="https://github.com/user-attachments/assets/1cb24882-15f9-4e9b-bd0c-e381f40a51ee" />


## Directory Enumeration

After we found the sub directory daloradius we try a recursive scan with ferox buster
![image](https://github.com/user-attachments/assets/b87beb8a-0c49-46a2-af37-fac4e3142c2a)

We find 2 login pages but we dont have any credentials for either
![image](https://github.com/user-attachments/assets/0a36b9bd-7af5-4b25-9c9a-50ff71f325f6)

**Pro Tip**: Whenever you encounter a login interface, it's good practice to test for default credentials. Many applications ship with preset logins that are often overlooked during deployment. Cross-reference with known default credential lists or official documentation to identify potential low-hanging access points.

![image](https://github.com/user-attachments/assets/0cd7ad4c-da00-4c2f-a777-4a5b464e355a)



