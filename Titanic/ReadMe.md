# Titanic - Linux(Easy)

## Summary
Titanic is an easy-difficulty Linux machine focused on web exploitation and leveraging misconfigurations. Initial access is achieved by discovering a Local File Inclusion (LFI) vulnerability in the ticket booking endpoint on the main website, which allows reading sensitive files and retrieving credentials for a developer account from Gitea. With these credentials, SSH access is obtained as the developer user. Privilege escalation is performed by exploiting a vulnerable ImageMagick installation (CVE-2024-41817) used by a root-executed script, enabling command execution as root and full system compromise.

## Nmap Enumeration



