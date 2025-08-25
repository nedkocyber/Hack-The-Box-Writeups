# Dog - Linux(Easy)

## Summary

Dog is an introductory Linux machine designed for beginners, offering a clear path from initial foothold to root. The machine begins with a publicly accessible Git repository containing sensitive information, which helps to gain access to a Backdrop CMS instance. This leads to remote code execution via file upload. Finally, privilege escalation is achieved through a misconfigured sudo command, allowing full root access.

## Nmap Enumeration

We start by performing an Nmap scan to enumerate open ports and identify running services. Initially, I used the -sCV flags for a full scan, but this approach is inefficient. A more effective methodology is to first run a default full port scan, then target the discovered ports with -sC for common vulnerabilities and -sV for version detection. This approach is faster and helps focus on relevant attack vectors.

<img width="1030" height="908" alt="image" src="https://github.com/user-attachments/assets/62d08d46-a1ac-483c-b792-5091ade7ed23" />

From the Nmap scan, we immediately notice a Git repository exposed on the web server. To ensure we have a comprehensive view of the attack surface, we run a Gobuster scan. This helps enumerate additional directories and files, potentially expanding our attack vectors and highlighting other entry points for exploitation.

## Directory Enumeration

<img width="1038" height="41" alt="image" src="https://github.com/user-attachments/assets/6edd25b2-2492-48d7-b0fa-1378d9db4b2d" />

From the Gobuster scan, we confirm the presence of the /.git directory.

<img width="720" height="21" alt="image" src="https://github.com/user-attachments/assets/49be0ee6-d57b-416e-8a1d-c50d87173036" />

After discovering the /.git directory, we attempt to dump it using git-dumper to extract the repository and search for sensitive information. It’s generally a good idea to download all the files locally, since browsing them directly over the internet may not reveal the complete repository structure, and important files or hidden details could easily be missed.

<img width="928" height="585" alt="image" src="https://github.com/user-attachments/assets/9831f7ec-8103-4fa0-95cb-24be6999883d" />

In the settings.php file, we find a hardcoded password.

<img width="870" height="106" alt="image" src="https://github.com/user-attachments/assets/a6c825c4-2e39-45c8-890c-43a0f032db86" />

And inside this configuration file we also discover a username

<img width="507" height="78" alt="image" src="https://github.com/user-attachments/assets/d04f34c1-6709-4c35-8d3c-b2afbb4e50f4" />

Creds --> tiffany@dog.htb:BackDropJ2024DS2024
Now we attempt to log in to the website using the discovered credentials for the user tiffany

<img width="1008" height="512" alt="image" src="https://github.com/user-attachments/assets/a4b6f7e1-4fcd-452a-9c40-8145d427eb94" />

After some digging, we discover the version of Backdrop CMS in use, and proceed to look it up for potential vulnerabilities.

<img width="1367" height="495" alt="image" src="https://github.com/user-attachments/assets/0a7e6042-28bb-4ba4-9608-885545cc1e1a" />

We find that this version is listed as vulnerable in the Exploit Database.

<img width="1029" height="508" alt="image" src="https://github.com/user-attachments/assets/f6ad9df4-3ca2-4907-b09e-fd5a59af5ac0" />

Since we need a .tar and not .zip   --> tar -cvf exploit.tar exploit.py

<img width="952" height="269" alt="image" src="https://github.com/user-attachments/assets/f0316110-804d-4352-b226-14c280e5a833" />

<img width="1295" height="598" alt="image" src="https://github.com/user-attachments/assets/d1be4429-ff27-44d9-899f-2573baddc7dc" />

<img width="1059" height="511" alt="image" src="https://github.com/user-attachments/assets/acbe4859-beaa-4172-a82d-eaf073a14163" />

Unfortunately, I forgot to document it, but the file needed to be .tar.gz; otherwise, it wouldn’t work properly.
<img width="486" height="52" alt="image" src="https://github.com/user-attachments/assets/41a31227-8c00-4d34-af26-26042062b0e5" />
<img width="986" height="401" alt="image" src="https://github.com/user-attachments/assets/5a1a7bfd-52ab-4a20-8b7c-cceabcff9a84" />

Now we execute the shell script file

<img width="809" height="433" alt="image" src="https://github.com/user-attachments/assets/de5eab4e-463f-4fa8-9be4-2fd444d2105d" />

And we pop a web shell

<img width="666" height="166" alt="image" src="https://github.com/user-attachments/assets/d3911e85-7e06-4788-8535-85678418127b" />

We retrieve the contents of /etc/passwd and identify potential user accounts. Using the password we discovered earlier, we attempt to log in to these accounts, hoping that one of them grants us access.

<img width="568" height="506" alt="image" src="https://github.com/user-attachments/assets/32951855-8c96-4dee-8fdc-8d5ec2dbe8ae" />

And we are successful with the user johncusack.

<img width="487" height="50" alt="image" src="https://github.com/user-attachments/assets/464bda89-b365-496a-8778-b08fede06400" />
We get the user flag.
<img width="196" height="25" alt="image" src="https://github.com/user-attachments/assets/5fae6b24-c0e3-4d07-b33c-fc91d2790056" />

## Privilege escalation

The privilege escalation on this machine is very straightforward.

<img width="1042" height="120" alt="image" src="https://github.com/user-attachments/assets/8a3fe241-6a04-4e02-bb96-a29b1de10ea2" />

The sudo -l output reveals that the user johncusack can run /usr/local/bin/bee with elevated privileges and without sudo password.

<img width="524" height="23" alt="image" src="https://github.com/user-attachments/assets/a9111c57-e8a7-49c3-9377-d07bbe0674c4" />
