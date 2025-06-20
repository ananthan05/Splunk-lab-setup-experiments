## Install Splunk Universal Forwarder on Victim
A. Download Splunk Universal Forwarder

Go to: https://www.splunk.com/en_us/download/universal-forwarder.html

![image](https://github.com/user-attachments/assets/198b428b-133f-480f-8891-607e2408fab7)

Download the .deb version (for Debian/Ubuntu):

```
sudo dpkg -i splunkforwarder.deb
```
![image](https://github.com/user-attachments/assets/9fa308a9-6d65-4d63-bd6f-4aa0ce05fc62)

### Configure Splunk Forwarder

Go to the `/opt/splunkforwarder/bin` directory

```
sudo ./splunk add forward-server 172.17.128.146:9997
```
create a username and passwaord to access splunk forwarder again.

![image](https://github.com/user-attachments/assets/5fc19e30-7034-416b-a52c-226b9534f53b)

```
sudo ./splunk set deploy-poll  172.17.128.146:8089
```

![image](https://github.com/user-attachments/assets/495cc8f2-e347-4745-a0c5-17a7fbcd255b)


Now run the splunk forwarder.

```
splunk start
```

Send Logs to Splunk Enterprise foe dvwa.

```
sudo ./splunk add monitor /var/log/apache2/access.log
```
```
sudo ./splunk add monitor /var/log/apache2/error.log
```

![image](https://github.com/user-attachments/assets/373b1ce2-52fb-4f7c-b96a-1472215614f4)

list the monitoring logs.

![image](https://github.com/user-attachments/assets/7629e441-c15b-4953-8391-9fad5984f254)

Now go and check in the Splunk enterprise to see if the victim is active.

![image](https://github.com/user-attachments/assets/eeaf296d-0a91-4b1b-91e5-1671fef33018)

Check if our logs are being captured.

![image](https://github.com/user-attachments/assets/1b93f25a-c223-4088-a751-e32ce069f64a)

DVWA doesnt log POST logs on apache logs so we need to add a custom post log for each vulnerabilities, so we will create a custom log and add it to all the vulnerabilities using bash command

sudo mousepad /var/www/html/DVWA/post_logger.php 

```
<?php
$log_file = '/var/log/dvwa_post.log';
$data = [
    'ip' => $_SERVER['REMOTE_ADDR'],
    'uri' => $_SERVER['REQUEST_URI'],
    'method' => $_SERVER['REQUEST_METHOD'],
    'post_data' => $_POST
];
file_put_contents($log_file, json_encode($data) . PHP_EOL, FILE_APPEND);
?>
```

Now to change the file permission

```
sudo touch /var/log/dvwa_post.log
sudo chmod 666 /var/log/dvwa_post.log
```

Now we can add it to each vulnerability files

```
find /var/www/html/DVWA/vulnerabilities/ -type f -name "*.php" -exec sed -i '1i<?php include_once("/var/www/html/DVWA/post_logger.php"); ?>' {} \;
```

We can use tail command to check the log after submitting a request

```
tail -f /var/log/dvwa_post.log
```

![image](https://github.com/user-attachments/assets/1c3442dd-4020-46e9-8237-50b494e409eb)

Now add this log to splunk for monitoring

```
sudo /opt/splunkforwarder/bin/./splunk add monitor /var/log/dvwa_post.log
```

![image](https://github.com/user-attachments/assets/b62996af-37c9-470a-82c2-e01545f86240)


