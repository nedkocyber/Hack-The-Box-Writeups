# Blocky - Linux(Easy)

## Summary
Blocky is an easy-level Linux machine that highlights insecure software development practices. Initial enumeration reveals a WordPress website, and directory brute-forcing uncovers a /plugins directory containing .jar files. Decompiling BlockyCore.jar exposes hardcoded MySQL credentials. Using a username from the blog and the recovered password, SSH access is gained as the user notch. Privilege escalation is straightforward, as notch has full sudo permissions, allowing root access via sudo su.
This machine is for all my Minecraft fans.

## Nmap Enumeration
Before we begin we add the target ip to our /etc/hosts file
First we do a full port scan   sudo nmap -p- <TargetIP> -T5 -o Blocky.txt
We get the ports 21,22,80,8192,25565 and we are doing more detailed scan for theese ports
![image](https://github.com/user-attachments/assets/5f47f289-71c0-45ec-8a4b-ed07c7f3cede)


## Directory Enumeration
![image](https://github.com/user-attachments/assets/2cf96355-5496-47eb-a287-cd0f7378609f)
In the directory plugins we find 2 files 
Because the file is encrypted we decrypt it and read the contents of it
![image](https://github.com/user-attachments/assets/2317c0b1-c307-4722-b5dd-801438a5436c)
After finding the credentials we log in phpmyadmin 

## Exploitation

![image](https://github.com/user-attachments/assets/30cb92b2-c458-4477-9cd0-59f9cb36e5a7)

And we find the user notch

![image](https://github.com/user-attachments/assets/a83c7457-f237-4bb4-9a92-172dfe62fd81)

Now we log via ssh with the credentials Notch:8YsqfCTnvxAUeduzjNSXe22

![image](https://github.com/user-attachments/assets/3632cbbd-b742-4dd5-ae42-e1583507c614)

<img width="193" alt="image" src="https://github.com/user-attachments/assets/360509ce-804e-43bf-a3be-3ac970d96a56" />

## Privilege Escalation
The password for root is the same as for the normal user so just sudo su type the password and cat the root flag

<img width="202" alt="image" src="https://github.com/user-attachments/assets/59ced4a0-a20d-4dea-ac42-9dcc5588e803" />
