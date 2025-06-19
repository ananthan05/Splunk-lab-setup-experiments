An authorization attack in DVWA occurs when a user gains access to resources or actions they are not permitted to, such as accessing another user's data by manipulating URL parameters.

So we need to try and access the users data after logging in as a non admin

![image](https://github.com/user-attachments/assets/fc285046-c3cb-4891-95e7-3a43922f1dc7)

We will try to login as user `gordonb` with password `abcd123` on burpsuite browser

![image](https://github.com/user-attachments/assets/633b5498-e771-4c08-baff-135fded32c2a)

After logging in we can see that the authorization bypass section is gone as it is not accessable by that user 

We will try pasting the `/vulnerabilities/authbypass/` in the url and try accessing it 

![image](https://github.com/user-attachments/assets/0c234e63-ec25-40f6-ba76-23857364affe)

It says unauthozied, eventhough the site has blocked acessing the html page lets check if files inside it could be accessed or not

In the source section it says there are php files inside the authbypass page

![image](https://github.com/user-attachments/assets/caa08ac3-637d-4472-9a87-faa4efffd4c1)


On burpsuite repeater page we will edit the path as `/vulnerabilities/authbypass/get_user_data.php`

![image](https://github.com/user-attachments/assets/5d9015bc-7df3-4093-a855-289aea0c6be7)

The user can sucessfully access it

Now we will try to update the details of employee

we changed the requset to POST

![image](https://github.com/user-attachments/assets/882c0ae2-917b-4a0d-b47d-fb2dede6e787)

It expects an id,firstname and lastname so we will give accordingly

![image](https://github.com/user-attachments/assets/901628bd-0fff-4cba-befd-918d18f9b3ea)

We will try to change the surname of Admin from 'Admin' to 'User'

![image](https://github.com/user-attachments/assets/55e7f536-2e10-456f-9519-953a0e227192)

![image](https://github.com/user-attachments/assets/c8289595-ac3e-43fe-b30f-4e7c95fe7ee7)

In the admin page also after refreshing we can see the name was changed, so attack successfull

In splunk logs apache/dvwa doesnt automatically log user login so we will add a custom log in change_user_details.php with this

```
session_start(); 
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $log_msg = sprintf(
        "[AUTHBYPASS] User=%s | SessionID=%s | IP=%s | Payload=%s",
        dvwaCurrentUser(),
        session_id(),
        $_SERVER['REMOTE_ADDR'],
        file_get_contents('php://input')
    );
    error_log($log_msg); 
}
```

Now when a user tries to update the value of the user details it will create a log and send to apache error log that we can access form splunk

by entering the command `host="victim" User=*` on splunk we can get the result

![image](https://github.com/user-attachments/assets/c9192fed-57d5-480f-8ab8-67cd3e75b7d9)


From here we can identify the user Gordon tried to update the user details table 

Also the input given by the admin `{"id":1,"first_name":"Admin","surname":"Admin"}` and by attacker `{\r\n"id": 1, "first_name": "Admin", "surname": "User"\r\n}` are slightly differnet

The `\r\n`,carriage return + newline usually comes from Tools like Burp Suite, custom scripts, or curl. So we could identify it was an attack
