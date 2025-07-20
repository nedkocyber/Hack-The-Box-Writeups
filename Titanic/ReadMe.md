# Titanic - Linux(Easy)

## Summary
Titanic is an easy-difficulty Linux machine focused on web exploitation and leveraging misconfigurations. Initial access is achieved by discovering a Local File Inclusion (LFI) vulnerability in the ticket booking endpoint on the main website, which allows reading sensitive files and retrieving credentials for a developer account from Gitea. With these credentials, SSH access is obtained as the developer user. Privilege escalation is performed by exploiting a vulnerable ImageMagick installation (CVE-2024-41817) used by a root-executed script, enabling command execution as root and full system compromise.

## Nmap Enumeration

First things first — we hit the target with a full port scan to catch any sneaky services hiding on high ports (think 55555 or above).
nmap -p- <target-ip> -T5

Once we’ve mapped all open ports with a targeted scan to pull detailed service info.
nmap -p 22,80 -sVC <target-ip>

<img width="1018" height="375" alt="image" src="https://github.com/user-attachments/assets/89ddb61b-a341-4ceb-86a8-6e563ce7d3bb" />

Nothing inturesting on the web application so we start looking for subdomains and sub directories

<img width="1032" height="373" alt="image" src="https://github.com/user-attachments/assets/5747546d-f46f-465f-b815-35f0ae32a2d4" />



