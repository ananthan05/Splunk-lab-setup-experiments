##  Launching an Attack from Another PC in the LAN:

In the Inspect tab (under browser developer tools), use the following payload in the input field:

```
' OR 1=1#
```

![image](https://github.com/user-attachments/assets/204ec059-5fea-496b-aa20-e0aec6a80745)


The attack was successful using that payload.

![image](https://github.com/user-attachments/assets/c59d897d-f986-45bd-b153-204ae190a6d2)


## Next, we will detect the attack using Splunk.

## 🔒 SQL Injection Detection: Logging POST Data in DVWA

### ❗ Problem

By default, Apache **does not log POST request body data** in `access.log`. This makes it difficult to detect SQL Injection and other attacks that rely on POST parameters.

---

### ✅ Solution

1. **Enable POST Request Logging**

To log POST parameters, you have two options:

- Use **web server-level logging** with tools like **ModSecurity**.
- OR, log **application-level data** (from the DVWA app itself).

---

### 🛠️ Modify DVWA Code to Log POST Data

Edit the file:

```
/var/www/html/DVWA/vulnerabilities/sqli/index.php
```

Add the following line near the top of the file, after the `<?php` tag and before any SQL execution:

```php
file_put_contents('/var/log/dvwa_post.log', "ID: " . $_POST['id'] . "\n", FILE_APPEND);
```

This logs each POSTed `id` value to a custom log file.

---



### 📈 Monitor the New Log File in Splunk

Use Splunk to monitor the new log file and detect attack patterns:

```bash
sudo splunk add monitor /var/log/dvwa_post.log
```

Now, Splunk will index all submitted IDs, including suspicious ones like:

```
' OR 1=1#
```

This makes it easier to detect and alert on **SQL injection attempts** via POST.

---

This approach gives visibility into POST-based attacks that wouldn't appear in standard Apache logs.


splunk command to dectect the attack-

```
host="victim" index=main source="/var/log/dvwa_post.log"
| regex _raw="(?i)(or\s+1=1|--|#|select|union|sleep|drop|insert|update)"
```


![image](https://github.com/user-attachments/assets/78685f8b-cc7c-48d4-98f8-423b59094aa8)

This confirms that **SQL injection attacks are being detected** through the enhanced logging method.
