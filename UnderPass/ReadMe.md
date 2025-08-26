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

## Exploitation

**Pro Tip**: Whenever you encounter a login interface, it's good practice to test for default credentials. Many applications ship with preset logins that are often overlooked during deployment. Cross-reference with known default credential lists or official documentation to identify potential low-hanging access points.

![image](https://github.com/user-attachments/assets/0cd7ad4c-da00-4c2f-a777-4a5b464e355a)

We find credentials in this github repo
![image](https://github.com/user-attachments/assets/e0ac235c-8d5d-491c-9af2-a4925887c3f7)

After logging in and looking whats on the web application we find a user and a hashed password 

Since crackstation is offline we will use netcat for cracking the hash

First we save the hash in a file 
Second we look up the hash type
Third we choose a wordlist and crack the hash

<img width="630" alt="image" src="https://github.com/user-attachments/assets/76d53901-5b61-4fd9-9907-7747a0f57212" />

When we see the status Cracked it means that we sucsessfuly cracked the password
![image](https://github.com/user-attachments/assets/1d455c01-6b30-43e8-8c5c-2baffc5112a4)

We get the password underwaterfriends

Now we connect to ssh with the credentials we got

![image](https://github.com/user-attachments/assets/832b1a0d-c177-477f-a130-6879501dc97a)

![image](https://github.com/user-attachments/assets/364ca2c6-a988-4c91-b757-8513ddf916ed)


## Privilege Escalation

![image](https://github.com/user-attachments/assets/7facda64-f8b0-49ba-9f3f-83c9989848dd)

With sudo -l we see that we are able to run /moshserver as sudo without the need of root password

After discovering the mosh-server binary on the system we executed a new Mosh session on a custom port

Using this key we connected to the session locally via mosh-client
![image](https://github.com/user-attachments/assets/7f583f2e-bb15-42ed-82d9-b9eda8a94b78)

This allowed me to spawn a detached terminal session as root

![image](https://github.com/user-attachments/assets/69b4b3e1-654f-48e3-83a8-0496a26f7775)


