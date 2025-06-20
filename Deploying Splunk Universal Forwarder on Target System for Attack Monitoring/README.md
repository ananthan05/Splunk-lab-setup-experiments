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
