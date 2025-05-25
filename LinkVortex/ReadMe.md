# LinkVortex - Linux(Easy)
## Summary
LinkVortex is an easy-difficulty Linux machine that emphasizes web enumeration and symbolic link exploitation. Initial access is obtained by discovering an exposed .git directory on a development subdomain, allowing retrieval of credentials for the Ghost CMS. Exploiting a vulnerability in Ghost (CVE-2023-40028), authenticated users can upload symbolic links to read arbitrary files within the container. This leads to the extraction of SSH credentials and user access. Privilege escalation is achieved by exploiting a symlink race condition (TOCTOU) in a script with sudo permissions, ultimately granting root access.

## Nmap Enumeration
![image](https://github.com/user-attachments/assets/f362a459-5d90-4e64-923b-c0259167acce)
 We see that 2 ports are open and we jump right into port 80
 Now we try reaching /robots.txt and we get some information from it
 ## Directory and Subdomain Enumeration
![image](https://github.com/user-attachments/assets/73784f58-9a36-48ed-924f-622c7430a8c8)

From here we navigate to /ghost and we get a login page and since we dont have more information we fuzz the domain for subdomains
![image](https://github.com/user-attachments/assets/e1965c11-4928-45e5-859a-8ecdc2891281)
we find 1 subdomain but still nothing of use to login into the page so we try brute forcing some directories
![image](https://github.com/user-attachments/assets/5c428817-372b-4fb4-a002-0cac23eeefdc)
after the directory brute forcing we find 1 usefull for us direcotry
![image](https://github.com/user-attachments/assets/26f48134-4546-4306-80a7-6ac76a08ec25)
At first nothing inturesting but its important to download all files from here because there can be hidden files
![image](https://github.com/user-attachments/assets/1b5c03f7-8604-46d5-af5c-7b32f914e7bf)
With that command we download all web content from the page and we start looking
![image](https://github.com/user-attachments/assets/add091a0-71b3-4c0f-86ac-3f4897b03f51)
this file is going to important in a bit

![image](https://github.com/user-attachments/assets/ed422f26-b45d-49a6-8e52-549cd1a45a56)

from this file we get a password

![image](https://github.com/user-attachments/assets/a949b60b-8fb7-476a-a585-42a3da2c97bc)

now we try logging in with the credentials
![image](https://github.com/user-attachments/assets/4fb2f393-6796-4de5-a1a4-7cf79b876697)
Important tip: Always try default credentials like root,admin,toor and etc

After logging in we satrt looking for important data like versions, public credentias and etc 
We find a Version for the application 
![image](https://github.com/user-attachments/assets/4e7b0ba8-dca9-48da-8259-aeeef251e55e)
Important tip:When we find a version we always check if its the latest version of the application because if its outdated there is a big possibility there will be an explooit for it 

We find a repo in github that shows how to use the exploit for our needs
![image](https://github.com/user-attachments/assets/646cd51a-4d44-4de2-90aa-76cfc680c5fe)
after succsesfuly running the exploit we can use the Dockerfile.ghost file we found earlier and paste the path to the file
![image](https://github.com/user-attachments/assets/da8c2c4b-d43c-4e7f-b8ac-f56ab0f66272)


after pasteing the lifepath we see credentials

![image](https://github.com/user-attachments/assets/f266e3be-ffb6-43af-bf4d-cbb7147e6639)

 Whit them we try logging via ssh and cat the user.txt flag
 <img width="197" alt="image" src="https://github.com/user-attachments/assets/bf9b9d6b-a719-480d-a0f3-1ba2f29a4eea" />
 ## Privilege escalation

 We see that we can run this without the need of root password
 ![image](https://github.com/user-attachments/assets/dcd6b9cf-beba-4dbe-99ca-db65e024a8a6)

 ![image](https://github.com/user-attachments/assets/31e483df-6ba4-4d68-9a78-be30963d08ad)

The main vulnerability in this script is command injection through the unchecked variable CHECK_CONTENT

Also another tip is if you are struggling with coding in general to use chatgpt or any AI to help you analyze the code for potential vulnerabilities 

Here we will use symlinks to read the contents of the root flag 
<img width="514" alt="image" src="https://github.com/user-attachments/assets/f4ebc07d-e5dc-46e1-8d89-0c628b9f9273" />


 















 
