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




## Password Spraying




## Access via WinRM




## Privilege Escalation

 
