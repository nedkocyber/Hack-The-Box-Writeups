# Cicada - Windows(Easy)

## Summary
Cicada is an easy-level Windows Active Directory machine that emphasizes fundamental enumeration and privilege escalation techniques. The challenge begins with SMB enumeration, revealing a share containing a plaintext password. This password, combined with user enumeration, allows for a successful password spray, granting initial access. Subsequent steps involve leveraging Active Directory attributes to uncover additional credentials and utilizing the SeBackupPrivilege to escalate privileges and obtain the Administrator hash.

## Nmap Enumeration

We start with a simple full port 

![image](https://github.com/user-attachments/assets/6b086cea-47d8-44ef-8492-868d24e8c689)

Then we run for default scripts and verions to see if we get something
![image](https://github.com/user-attachments/assets/13532c71-1362-4e98-aa2d-e26dfa0aaf24)

We see that we have SMB open so we start with it

## SMB Enumeration

![image](https://github.com/user-attachments/assets/d2f280e8-139a-4e2a-ad46-45c247fa8063)

After connecting to port 445(SMB) We see that we have a file Notice from HR.txt
![image](https://github.com/user-attachments/assets/d5737ac6-fdee-4371-a3c6-be2fe8d787be)

With the command get <filename> we download it to our machine
![image](https://github.com/user-attachments/assets/15c2d5ad-ec57-4fa2-99dd-7b7f762d768c)

We find a password inside the file but we dont have any users to try the password on 
![image](https://github.com/user-attachments/assets/e5be1fb2-cf4a-4958-983a-92e685df58df)


## User Enumeration
![image](https://github.com/user-attachments/assets/a18040cf-ead2-480a-bab3-706e36c08f7d)
Now we bruteforce RIDs using Guest authentication

We get a couple users
![image](https://github.com/user-attachments/assets/2ec27c1a-e87f-4d8e-859c-a1f097ab997f)
after trying all users with the password we see that with the michael user we have some usefull information

![image](https://github.com/user-attachments/assets/fe5e6b06-48a0-4f09-9236-62ea0367b259)

with the tool smbmap we can see what shares are open to us with the credentials we got
![image](https://github.com/user-attachments/assets/21b064c0-ab9e-41fe-a73e-54c40a9b1ec6)

Now we connect to smb again but to DEV folder
![image](https://github.com/user-attachments/assets/6df7b8b6-9ab0-4321-914f-dad513516dee)

there we have a file that contains user and a password that we try to connect to

![image](https://github.com/user-attachments/assets/96072891-0995-430d-a3a6-8708cf49b057)

## Access via WinRM

![image](https://github.com/user-attachments/assets/9d1958cc-4e3a-4b7d-8075-88d8922b7611)

<img width="416" alt="image" src="https://github.com/user-attachments/assets/c5b7264c-d9c3-430c-8ac6-10e4f333ba6a" />


## Privilege Escalation

 ![image](https://github.com/user-attachments/assets/6e33ffe1-08d2-4d82-ad04-54e9b2af3abb)
Here we can have SeBackupPrivilege enabled and that means we can dump SAM and SYSTEM hives

![image](https://github.com/user-attachments/assets/5d73d452-cff9-4de0-9ba3-0e4927476313)


after that we just download both files

![image](https://github.com/user-attachments/assets/b3d2fe9a-da32-41c3-9738-ffb62952eefd)
![image](https://github.com/user-attachments/assets/c33fa87c-17f3-4dc4-bee5-ad2c8b346287)

After we downloaded both files on our machine we can begin extracting the administrator NTLM hash with pypykatz
![image](https://github.com/user-attachments/assets/87320faa-12d5-4c24-9e5a-39993b9d5c6b)

When we have the hash we can use Pass The Hash attack to login with evil-winrm
![image](https://github.com/user-attachments/assets/1acb74d0-574b-4cfb-aeb9-ce1dae37d9cf)

<img width="386" alt="image" src="https://github.com/user-attachments/assets/58dbe930-e3a0-4d14-9193-071cededbf2a" />
