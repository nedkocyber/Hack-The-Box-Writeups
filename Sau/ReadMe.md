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




