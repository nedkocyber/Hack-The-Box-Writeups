# Nocturnal - Linux(Easy)
## Nmap Enumeration

First thing I do is drop the target’s IP into /etc/hosts.

<img width="1030" height="883" alt="image" src="https://github.com/user-attachments/assets/a04b7e35-d06f-4b1b-8a45-4a43a1554961" />

## Directory Enumeration 

Always make sure to save your scans into a file — one slip and you close your terminal, you’ll have to start all over again. Efficiency is key. While tools like Gobuster are running, don’t just sit and wait. Use that time to poke around the web application, try some functionality, see what’s exposed. Multitasking keeps you moving forward instead of wasting precious time staring at progress bars.

<img width="1045" height="458" alt="image" src="https://github.com/user-attachments/assets/189e8cf1-b468-4b7d-8e35-95a90538385b" />

At this stage, we’re presented with the option to either log in or register. Naturally, we proceed with that to observe the application’s behavior and uncover what might come next.

<img width="1041" height="506" alt="image" src="https://github.com/user-attachments/assets/d657e98b-7228-4caf-8e95-2f37cbcf7b99" />

<img width="991" height="505" alt="image" src="https://github.com/user-attachments/assets/f4e0e7cd-235e-452c-8e52-111288a56356" />


## Exploitation
Upon logging in, I immediately identified a file upload functionality. The most straightforward approach in such scenarios is to attempt uploading a reverse shell to establish initial access.

<img width="868" height="548" alt="image" src="https://github.com/user-attachments/assets/7c7ddeba-0365-4761-9bad-495e303dc499" />

Unfortunately, the upload functionality enforced restrictions, allowing only specific file types to be uploaded.

<img width="681" height="30" alt="image" src="https://github.com/user-attachments/assets/dfd4d26d-6906-440c-8cf1-01e4b86815aa" />

Important tip: Always review the page source, as developers may unintentionally leave behind sensitive information such as credentials, version numbers, or configuration details. Such data can provide attackers with an easier path to unauthorized access.

<img width="873" height="650" alt="image" src="https://github.com/user-attachments/assets/b0f43696-ba3b-440d-9415-95814f7de07a" />

We attempt to upload a simple test.pdf file in order to analyze the server’s response and determine how it handles file submissions.

<img width="1014" height="598" alt="image" src="https://github.com/user-attachments/assets/378715f6-fa19-42de-bf18-7733c5f6875d" />

From here, we notice that the application exposes username and file parameters.

We now use Burp Suite to intercept the request and make slight modifications, allowing us to observe how the server responds.

<img width="1391" height="676" alt="image" src="https://github.com/user-attachments/assets/b47853a9-dd44-4523-a190-0b9e6b93bb15" />

After analyzing the intercepted requests, we identify the username amanda. We confirm it as valid because the response length differs from other usernames, indicating that the server processes it differently

<img width="1347" height="639" alt="image" src="https://github.com/user-attachments/assets/fa267623-ef5b-4015-bca0-9cc6e9711ea7" />

Now we discover that the user has a secret file. To obtain it, we simply remove view-source: from the URL and download the file directly.

<img width="1129" height="731" alt="image" src="https://github.com/user-attachments/assets/377c3ce4-835f-457c-aafa-473ff4eefd7f" />

Inside this file, we find a password. It’s a good practice to store discovered credentials securely, as they can often be reused across different accounts or services during the engagement.

<img width="976" height="342" alt="image" src="https://github.com/user-attachments/assets/c209af6b-7003-46ba-9ad0-b8d09b1768cb" />

We access Amanda’s profile and create a full backup of all available data.

<img width="981" height="711" alt="image" src="https://github.com/user-attachments/assets/1bc4e34a-21e6-4b89-ace8-69094b78a629" />
<img width="1023" height="640" alt="image" src="https://github.com/user-attachments/assets/e4a0be43-1524-4302-8bba-682a557199c3" />

After downloading the backup file, we unzip it and begin inspecting its contents for sensitive or valuable information.

<img width="576" height="74" alt="image" src="https://github.com/user-attachments/assets/d2345b9e-c08c-435e-958d-aec2e6980b6f" />

In admin.php, we notice a basic command injection filter. We plan to attempt bypassing it to execute arbitrary commands and test the extent of the vulnerability.

<img width="1026" height="554" alt="image" src="https://github.com/user-attachments/assets/3780f14d-4f7e-4874-a63d-335079504ca1" />

We intercept the request just before initiating the backup to analyze its structure and parameters.

<img width="1026" height="583" alt="image" src="https://github.com/user-attachments/assets/dbdf335a-fd82-4739-a425-9c7ec0340538" />

We notice that we can inject commands by using the double quote (") character.

<img width="1032" height="492" alt="image" src="https://github.com/user-attachments/assets/6bc80b45-aac3-4d3b-bfc4-bbc2c2365f8f" />

Next, we attempt to upload a reverse shell to gain access.

<img width="1015" height="414" alt="image" src="https://github.com/user-attachments/assets/2bda7937-c2a0-40a9-951d-d4779e958b2a" />

A key detail we initially missed is in view.php: it reveals the database type and the path to the .db file.

<img width="834" height="65" alt="image" src="https://github.com/user-attachments/assets/5897b609-3178-4d9f-ad54-dfe856cea9b0" />

Now that we know the exact location, we navigate to the database file and extract the credentials.

<img width="886" height="675" alt="image" src="https://github.com/user-attachments/assets/ee485f36-4bca-4fc0-b533-fb976bfb98a1" />

Next, we take the hashed password and use CrackStation to recover the plaintext.

<img width="943" height="293" alt="image" src="https://github.com/user-attachments/assets/48ba4465-7276-4bfc-878e-9c7af9329c20" />

Using these credentials, we now log in via SSH as the user tobias.

<img width="470" height="48" alt="image" src="https://github.com/user-attachments/assets/a9771f05-c002-436d-94c0-9d940775c587" />

And capture the user flag

<img width="396" height="43" alt="image" src="https://github.com/user-attachments/assets/0531657a-2224-4f32-aa25-da459ba01b1e" />

## Privilege escalation

We notice that there’s a service running on localhost:8080. To investigate it, we set up port forwarding, allowing us to access the service from our machine and analyze its behavior.

<img width="1202" height="175" alt="image" src="https://github.com/user-attachments/assets/53284786-6ea8-46e7-8722-c67e68766614" />
<img width="599" height="57" alt="image" src="https://github.com/user-attachments/assets/814aff67-c65d-4589-a404-a0022589ab0c" />

Using the credentials for admin and the password from tobias, we are able to successfully log in.

<img width="912" height="589" alt="image" src="https://github.com/user-attachments/assets/1d811a84-be62-4b0a-a976-f4e90883a629" />
<img width="1005" height="588" alt="image" src="https://github.com/user-attachments/assets/ed91e41a-c331-4354-b69f-4bf8da199f39" />

We identify the version of the software in use and search for known exploits targeting that specific version.

<img width="490" height="125" alt="image" src="https://github.com/user-attachments/assets/fd0929ac-4f31-4824-a062-fd7803f7e0d9" />

We locate a suitable exploit on GitHub for the identified version.

<img width="1204" height="581" alt="image" src="https://github.com/user-attachments/assets/8c48b6cf-b934-4cec-8995-4a041f67fb3c" />
<img width="654" height="146" alt="image" src="https://github.com/user-attachments/assets/7c0c327e-6365-4667-8a73-635015d37766" />

After executing the exploit, we successfully gain root access and retrieve the root flag, completing the machine and confirming full system compromise.

<img width="977" height="317" alt="image" src="https://github.com/user-attachments/assets/e1bbcfb0-6f24-4231-9bb3-bdfc191db491" />
<img width="212" height="59" alt="image" src="https://github.com/user-attachments/assets/2e9c6bc4-7dad-4ae0-a474-9c691a737de1" />

