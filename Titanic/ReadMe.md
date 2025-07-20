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

## Web Enumeration

Now we fuzz for a subdomain and find theese two
<img width="1032" height="373" alt="image" src="https://github.com/user-attachments/assets/5747546d-f46f-465f-b815-35f0ae32a2d4" />

After navigating to it we find this page

<img width="1037" height="518" alt="image" src="https://github.com/user-attachments/assets/da44793a-e518-4bd8-8d45-f38f0ae930bd" />

Pro Tip: Always dig through the website’s source code — you’d be surprised how often devs leave hardcoded admin creds, API keys, or juicy info like app versions just lying around.

<img width="1057" height="485" alt="image" src="https://github.com/user-attachments/assets/30cb1f03-1940-46de-aa7b-5189b7d7c8e3" />

We make a quick new account and continue

<img width="1042" height="200" alt="image" src="https://github.com/user-attachments/assets/96030f13-5bf9-4890-86ef-0e99b1f0f3e7" />

After looking around for a bit we find in this repo valuable information

<img width="1016" height="336" alt="image" src="https://github.com/user-attachments/assets/7079a86b-5580-4dff-ab59-3f64786ae3cb" />

Pro Tip: If coding isn’t your strong suit, just grab the code, drop it into ChatGPT, and ask it to sniff out potential vulnerabilities — saves time and catches things you might miss.

In this case, the Python code lacks proper path validation.
The application does not sanitize or normalize user input, nor does it verify that the requested file resides within the intended directory. As a result, directory traversal (../) can be used to access arbitrary files outside the application’s working directory.

<img width="1025" height="875" alt="image" src="https://github.com/user-attachments/assets/55197955-d87d-4efb-857a-d18a7404fede" />

On the Gitea site, we look for a configuration file that might help us with something

<img width="1049" height="349" alt="image" src="https://github.com/user-attachments/assets/807bc281-8b17-4997-ae93-bce6f1150bb3" />

Using the path we discovered, we access and review the contents of the app.ini file.

<img width="1028" height="731" alt="image" src="https://github.com/user-attachments/assets/5b30d28d-c689-4cc3-b145-e1abb04ff750" />

Here we Download the contents of the app.ini file

<img width="1049" height="91" alt="image" src="https://github.com/user-attachments/assets/b1352159-d87b-4f67-baa2-c84cd3816a52" />

<img width="1042" height="63" alt="image" src="https://github.com/user-attachments/assets/2fb2860b-63ee-4bdc-94e6-3b8b7d8ba643" />

Tip: I ran into issues downloading the file and reading its contents because I didn’t realize the desktop directory had global write permissions. Also, the /tmp directory wasn’t available for this, so I had to adjust my approach accordingly.

<img width="510" height="19" alt="image" src="https://github.com/user-attachments/assets/8d171072-1876-49d2-9021-931ced8ec59b" />
<img width="539" height="20" alt="image" src="https://github.com/user-attachments/assets/b7a50aea-ad55-423a-9dcb-2a67dd9ca933" />

After cracking the hash we get the password
<img width="1029" height="52" alt="image" src="https://github.com/user-attachments/assets/68fc61a6-963c-4a6f-90fc-912289f21203" />

Now with the password and the name we got we log via ssh and get the user flag
<img width="958" height="862" alt="image" src="https://github.com/user-attachments/assets/bd8399f7-0a75-4a23-b5f7-a6de3e3e91ba" />
<img width="200" height="24" alt="image" src="https://github.com/user-attachments/assets/4af727ae-015d-4fe9-bdd0-1695d7853930" />

## Privilege Escalation

<img width="648" height="201" alt="image" src="https://github.com/user-attachments/assets/c87ba57e-11c0-48bf-b017-99c7fbde7f64" />

With this command, we identify which files or directories are writable—this is crucial information when attempting privilege escalation

<img width="1018" height="80" alt="image" src="https://github.com/user-attachments/assets/bf8d555c-866f-4730-b6b1-a97dd031d16a" />
After that we find this file in /opt/scripts 


This script automates the collection of image metadata by enumerating all .jpg files in the specified directory. It utilizes the identify tool from ImageMagick to extract relevant properties, storing the results in metadata.log.

<img width="1046" height="116" alt="image" src="https://github.com/user-attachments/assets/8789860a-00ce-4bb9-b387-e13b9cfa5279" />

Pro Tip: Always research for known vulnerabilities, either on GitHub repositories or across the web in general. Staying aware of public exploits or misconfigurations can save a lot of time during testing.






