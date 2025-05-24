# TwoMillion - Linux (Easy)
## Nmap Enumeration
We begin with a simple nmap scan to see what ports are open
![Image](https://github.com/user-attachments/assets/2a4fad03-3938-45d8-b874-909f2adf62ca)
After that add the targets machine ip to our /etc/hosts file
![Image](https://github.com/user-attachments/assets/555552a1-d2e0-4bc1-9a8f-2f604bcf078f)
## Directory enumeration
Now with the help of any directory brute forcing tool we look up a couple directories
![Image](https://github.com/user-attachments/assets/61811d99-6796-471d-a061-b716e462c045)
We see a couple directories but /js/inviteapi.min.js cought my attention
![Image](https://github.com/user-attachments/assets/b8bc79b3-f4e6-4e53-9635-93387f548b5d)
Navigting to it shows a JS(JavaScript) code that does a couple things
and one of the important things is that it sends AJAX request to /api/v1/invite/how/to/generate and the methood is POST




