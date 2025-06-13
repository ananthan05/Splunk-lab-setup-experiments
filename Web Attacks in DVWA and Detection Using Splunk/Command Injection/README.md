Launching an Attack from Another PC in the LAN:

```
172.17.128.158 & hostname & ls
```

The attack was successful.

![image](https://github.com/user-attachments/assets/54f1cd3e-6d28-40c2-82b5-d5217dfec731)

Attack successful. Now, try to detect it from Splunk Enterprise:

command 

```
host="victim" source="/var/log/apache2/access.log" sourcetype=access_combined  
| regex uri_query=".*(\&|\||;|`|\$).*"
```

![image](https://github.com/user-attachments/assets/36dd4b40-a3e9-41f3-8190-77ec5b4854d9)
