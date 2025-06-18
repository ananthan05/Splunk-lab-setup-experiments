Stored XSS occurs when a malicious script is permanently saved on the server (e.g., in a database) and executed in users' browsers when they view the affected content.

We will try entering the payload `<script>alert(1)</script>` in the message field

![image](https://github.com/user-attachments/assets/9e7c82c1-791e-4ac3-8c4d-a609f2e644c6)

After clicking sign guestbook no pop up alert was triggered

![image](https://github.com/user-attachments/assets/27e13dbb-e921-4340-8e11-0508d0132225)

The site sanitizes the input values so we have to change the payload

![image](https://github.com/user-attachments/assets/6e0e25a5-cdcd-4e16-9fa9-3322856374a1)

We will try with the payoad `<Script>alert(1)</Script>`

![image](https://github.com/user-attachments/assets/673055ca-5be7-4148-bebe-1c65c3d2293e)

No alert was triggedred here also, we will try placing alert in the name field

For that we need to chnage the maxlength of name field from 10 to 50 through the inspect option in browser

![Screenshot 2025-06-18 102819](https://github.com/user-attachments/assets/0820732f-eaaa-4cbd-a95d-5bd6fa26974f)

Now when clicked on sign guestbook button alert will be triggered

![Screenshot 2025-06-18 102935](https://github.com/user-attachments/assets/dfde014c-ddf3-4384-a1ad-1f1c24aa6b05)

Attack successfull, now to detect it in splunk

![image](https://github.com/user-attachments/assets/a87baae3-a9fc-4cfd-ad67-0133b045adf7)

We add splunk command that searches for common xss payload type like `script`

On defualt the dvwa access logs doesnt show content in the post request so we need to change the php file related to the attack inorder to log the post files

We need to change the medium.php file in victim pc at the path /var/www/html/DVWA/vulnerabilities/xss_s/source  with this command 

```
file_put_contents(
    '/var/log/dvwa_post.log',
    sprintf(
        "IP: %s - Name: %s - Message: %s\n",
        $_SERVER['REMOTE_ADDR'],
        $_POST['txtName'],
        $_POST['mtxMessage']
    ),
    FILE_APPEND
);
```
![WhatsApp Image 2025-06-18 at 2 19 04 PM](https://github.com/user-attachments/assets/ecbbdc56-7ccb-417f-b603-7231e61defb2)

Now we can get the post request contents in splunk

We can use splunk command to list the logs with script payload in it using 

`host="victim" | regex _raw="(?i)(<script>|onerror=|<img|alert\()"`

![image](https://github.com/user-attachments/assets/a1531982-6770-4044-bc8b-b9eb851aaa45)

from this the ip and the script used by attacker is visible

![Screenshot 2025-06-18 143851](https://github.com/user-attachments/assets/5c05d56c-0970-4b99-b4c6-ecc0c1c348d5)


The payload persists across page loads.

It's submitted through a form (not just a URL).

It auto-triggers when the page is visited again.

It shows up in logs saved from a POST request (not GET)

So from this we can understand it is a Stored XSS attack

