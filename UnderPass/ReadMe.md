# UnderPass -Linux(Easy)

## Summary
UnderPass is an easy-level Linux machine that emphasizes comprehensive enumeration and privilege escalation techniques. The challenge begins with identifying open TCP ports, leading to the discovery of an SNMP service. Through SNMP enumeration, a daloRADIUS web interface is uncovered, accessible via default credentials. Within this interface, a password hash for the user svcMosh is found and subsequently cracked, granting SSH access. Privilege escalation is achieved by exploiting the misconfigured mosh-server, which allows the user to execute it with root privileges, ultimately leading to a root shell.

## Nmap Enumeration
We start with a full port scan and after we see what ports are open we scan them for versions and default scripts

![image](https://github.com/user-attachments/assets/956216aa-7af6-43b9-8b95-b672a92411a6)

## Snmp Enumeration


## Directory Enumeration
Since there is nothing on port 80 we try scaning for sub directories





