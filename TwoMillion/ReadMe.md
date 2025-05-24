# TwoMillion - Linux (Easy)
## Summary
TwoMillion is a easy-difficulty Linux machine on Hack The Box that involves web enumeration, JavaScript analysis, and API exploitation. After discovering a hidden invite generation endpoint, the user leverages BurpSuite to analyze HTTP requests and eventually gains a foothold via a reverse shell. Privilege escalation is achieved by exploiting a known kernel vulnerability, leading to root access.
## Nmap Enumeration

We begin with a simple nmap scan to see what ports are open
![Image](https://github.com/user-attachments/assets/2a4fad03-3938-45d8-b874-909f2adf62ca)
After that add the targets machine ip to our /etc/hosts file
![Image](https://github.com/user-attachments/assets/555552a1-d2e0-4bc1-9a8f-2f604bcf078f)
## Directory enumeration

Now with the help of any directory brute forcing tool we look up a couple directories
![Image](https://github.com/user-attachments/assets/61811d99-6796-471d-a061-b716e462c045)
We see a couple directories but /js/inviteapi.min.js cought my attention
## Finding vulnerability

![Image](https://github.com/user-attachments/assets/b8bc79b3-f4e6-4e53-9635-93387f548b5d)
Navigting to it shows a JS(JavaScript) code that does a couple things
and one of the important things is that it sends AJAX request to /api/v1/invite/how/to/generate and the methood is POST
Now with curl we can look up the directory /api/v1/invite/how/to/generate and we get a message 

## Exploiting the vulnerability
![Image](https://github.com/user-attachments/assets/961d29c7-9c42-4eb9-8771-4941c1051597)
Now with the help of a site called CyberChef we can decrypt the message
![Image](https://github.com/user-attachments/assets/5193ac1a-91fa-4116-8bc3-f569bb431701)
again with curl we send a POST request to the directory we got
![Image](https://github.com/user-attachments/assets/2d5be34a-a151-432d-80dc-5ab6e3a5f0c2)
Decoding it again with CyberChef
![Image](https://github.com/user-attachments/assets/d5d18897-af9b-4819-aa34-518df9ad1ab0)
With the invite code we have now we try making a registration to /register
![Image](https://github.com/user-attachments/assets/07e6f324-4ae8-42ae-be2b-c31adf7b4255)

After that we login and start looking for any usefull information like applicaton versions changeing rights and etc
Here we have a weak point in the application and before we hit the button Regenerate we start burp suite and catch the request to look it up

![Image](https://github.com/user-attachments/assets/a8b1e1f9-3a93-46f9-bb54-54ed5eb71396)

with the request that we just caught we send it to the repeater 

![Image](https://github.com/user-attachments/assets/cbc6ea65-8683-4439-9f77-3028a24c2da2)

after sending the request to the repeater a couple inturesting things pop up

![Image](https://github.com/user-attachments/assets/76c4be23-b0e1-486b-855d-1c492cb3b3c8)

navigating to /api/v1/admin/settings/update leads to acouple missing parameters that we try and fill

![Image](https://github.com/user-attachments/assets/19cbc2e7-1310-47e1-8cfe-b43394c263b5)


![Image](https://github.com/user-attachments/assets/180b8c7a-da92-4656-8dff-ffe2929d5eca)

![Image](https://github.com/user-attachments/assets/259a1b78-b307-4b84-9120-6cb9c3e8e94e)

now we see that we successfully created a vpn and we are going to try an injection attack that gives us a shell
we will generate a shell with a site called revshells.com

![Image](https://github.com/user-attachments/assets/555153fc-fd4b-4e72-95b0-024f362a0220)

with a little modification to the burp request and a netcat listner we are able to get a shell

![image](https://github.com/user-attachments/assets/a7feb3ea-4646-46a9-909f-1be49c272b4e)

now with the shell we start looking for usefull information and we find it in the env file

![image](https://github.com/user-attachments/assets/07d235c4-40b1-457a-9878-e3815a835eb7)

with the givven creds we login with ssh and get the user flag

<img width="203" alt="image" src="https://github.com/user-attachments/assets/9a16f6cc-833b-41f7-993d-09e3c5250922" />

## privilege escalation
![image](https://github.com/user-attachments/assets/8fdb91f6-6650-435e-b795-f185494ee1ab)

with the given version we look it up for any vulnerabilities and we find this CVE

![image](https://github.com/user-attachments/assets/b107dcfa-d970-4de9-8256-acebae7d726b)

after downloading the CVE with the help of a python server we are going to transfer it to our target
![image](https://github.com/user-attachments/assets/7a2fb9b0-8ee0-4190-98b4-a2865bcaaa37)
![image](https://github.com/user-attachments/assets/a72ee8e8-09c0-4535-8e58-a2253e68e361)

after following the instructions on the github repo we are able go get root accsess and capture the root flag
![image](https://github.com/user-attachments/assets/7b4290d0-2601-4cd2-8d35-9a1343b857e2)

<img width="216" alt="image" src="https://github.com/user-attachments/assets/4adaee00-0d7b-43b4-a503-bd3b82d3b1c8" />


