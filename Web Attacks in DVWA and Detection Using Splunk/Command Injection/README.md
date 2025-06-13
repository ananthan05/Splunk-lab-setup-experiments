Launching an Attack from Another PC in the LAN:

```
172.17.128.158 & hostname & ls
```

The attack was successful.

![image](https://github.com/user-attachments/assets/54f1cd3e-6d28-40c2-82b5-d5217dfec731)

Attack successful. Now, try to detect it from Splunk Enterprise:

command 

our regex is flagging uri_query values like:

id=8;ls
id=7|whoami
id=5`uname -a`
id=4&echo Hello

These are typical payloads for command injection.

If you see one of these in your logs, that’s a strong indicator of an attempted command injection.

```
host="victim" source="/var/log/apache2/access.log" sourcetype=access_combined  
| regex uri_query=".*(\&|\||;|`|\$).*"
```

![image](https://github.com/user-attachments/assets/36dd4b40-a3e9-41f3-8190-77ec5b4854d9)

![image](https://github.com/user-attachments/assets/6b2758d0-9604-4c00-aad2-c4d29a437420)

Two logs were found when we searched using the regex flag, and we can see that the bytes value is over 1000—specifically, it's showing 2066. If the bytes are significantly larger or different from normal, it could indicate that command output was returned. For example, id=ls and bytes=3000 where normal is bytes=1000, suggests that something was likely executed. This indicates an attempt at command injection.
