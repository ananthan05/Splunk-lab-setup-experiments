##  Launching an Attack from Another PC in the LAN:
 
We start by launching the CSRF attack in DVWA with the security level set to 'low'. This allows us to observe how the attack is structured and executed. To do this, we send a request to change the user's password as a test, demonstrating how an attacker could exploit the vulnerability.

![Screenshot 2025-06-17 142638](https://github.com/user-attachments/assets/4eaf40f0-ba60-4814-ac1e-7b854129cf04)

Now, check the URL — we can see the query that was used to change the password

![image](https://github.com/user-attachments/assets/e6a31431-78a8-48de-a6c4-11d6c558beec)

By modifying the URL directly, we can perform the attack. For example, changing the following URL:

```
http://172.17.128.158/DVWA/vulnerabilities/csrf/?password_new=test&password_conf=test&Change=Change#
```
to this:

```
http://172.17.128.158/DVWA/vulnerabilities/csrf/?password_new=test123&password_conf=test123&Change=Change#
```
will change the user's password to test123. This demonstrates how the CSRF vulnerability can be exploited just by tricking the user into visiting a malicious link.

![image](https://github.com/user-attachments/assets/4d0400e9-3cde-4553-9d73-36145033a841)

We can test this using the test credentials option in DVWA.

![image](https://github.com/user-attachments/assets/0e2e15f8-86a5-438f-9aa5-74a36ad0c938)

Now, change the security level to 'medium' and try to perform the attack again. If we try using the same URL method, it won't work.

![image](https://github.com/user-attachments/assets/dd950386-1f7a-4846-b93e-5dfea9d39742)

First, we need to open Burp Suite and capture a normal password change request.

![image](https://github.com/user-attachments/assets/04550558-ef71-48d7-89de-d26f7d6cb62b)

Now, send the captured request to Repeater in Burp Suite and modify the parameters to change the password to test1234. Then, click "Send" to observe the response and confirm whether the password change was successful.

![image](https://github.com/user-attachments/assets/cb005beb-721f-4e11-aed9-0d56fe5e8d63)

The response shows that the request was successful, indicated by the response code. Now, go back and verify that the password has been changed by logging in with the new password test1234 using the Credential Tester option in DVWA.

![image](https://github.com/user-attachments/assets/3f1a19db-6ae3-415e-8344-84dcb8205fb3)

The attack was successful.

## Next, we will detect the CSRF attack using Splunk.

1. Check for Token Reuse.

```
host="victim" uri_path="/DVWA/vulnerabilities/csrf/"
| spath input=request_body path=user_token
| stats count by user_token, clientip
| where count > 1
```

![image](https://github.com/user-attachments/assets/891d7e18-5c3b-4d02-a4d3-39f9ca8e6ae4)

with the table shown -

 Token Reuse Across Multiple Requests

`7d15ecc86c1b46c44467d882cb2c2121` used 10 times → strong indication of a replay attack.

Real users typically use a CSRF token once (it's single-use).

 Another Token Reused 3 Times

Still suspicious; normal behavior would show only one use per token.

Blank Count for One Token

e7b8... shows no count — might be malformed, truncated, or the count column wasn't filled.



 
2. Group Requests by Minute
To visualize burst patterns:

```
host="victim" clientip="172.17.128.21" uri_path="/DVWA/vulnerabilities/csrf/"
| bucket _time span=1m
| stats count by _time
```

![image](https://github.com/user-attachments/assets/d95a083d-22b4-40d2-8ac4-627d878ddc4a)

we can say that- 

- **Client IP `172.17.128.21`** is identified as the attacker.
- This client is repeatedly resending the **same CSRF token**, likely using Burp Suite's Repeater or an automated script.
- This behavior **bypasses CSRF protection** if the web application:
  - Does **not invalidate** the token after a single use.
  - Does **not validate** the `Referrer` or `Origin` headers.


3. Confirming Successful Password Change:

Look for POSTs where the token was reused and password changed:

```
host="victim" uri_path="/DVWA/vulnerabilities/csrf/"
| search uri_query="*password_new=*"
| table _time clientip user_token  uri_query useragent
```

![image](https://github.com/user-attachments/assets/b1a99ce1-6791-4601-a0c7-245492790a04)

### Observed Activity:

We can clearly see the CSRF-based password change activity in Splunk, including:

- The exact new passwords attempted (`password_new`)
- Whether a `user_token` was present (indicating DVWA security level: 
  - Low/Medium → no token
  - High → token required)
- All requests originated from the IP address: `172.17.128.21`

### 🔍 Interpretation:

- Client IP `172.17.128.21` repeatedly attempted to change passwords using direct GET requests.
- Most of these requests lack a `user_token`, suggesting DVWA was set to **low** or **medium** security levels, where CSRF protection is weak or missing.
- The requests appear to be **automated or scripted**, as they are repeated and only the `password_new` value changes.

### ✅ Conclusion:

The CSRF attack was successfully detected using **Splunk**.
