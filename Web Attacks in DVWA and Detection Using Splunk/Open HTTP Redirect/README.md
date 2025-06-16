Open the victims dvwa site on the burpsuite browser

![image](https://github.com/user-attachments/assets/faccdc15-7035-4030-8df3-73ace7e3fda3)

Turn on the intercepter and select the highlighted link

Now open the intercepted file on the repeater

![image](https://github.com/user-attachments/assets/aa11cbfb-2f04-42d2-99cb-28a2e1759a29)

Firstly we will try adding an absolute url as the redirect path eg `https://www.google.com`

We will copy this path and input it as the target redirect url into the captured request

![image](https://github.com/user-attachments/assets/8f15d35a-eb51-40e6-b829-431c611c051b)

It gave an internal 500 error stating that absolute urls are not allowed

Now we will try giving relative Protocol relative url to see if redirection occurs.Protocol relative urls are a link to a URL without specifying the scheme (eg without the 'https:')

![image](https://github.com/user-attachments/assets/6f311a18-3fa9-4438-8288-bde9877efaf8)

When the url `//www.google.com` was given the redirection happened as it is not an absolute url but a protocol-relative url, also the status 302 on the rightside indicates that the redirection happened successfully

we can copy paste the payload url into the server to get the redirection

`/DVWA/vulnerabilities/open_redirect/source/medium.php?redirect=//www.google.com?id=1`

![image](https://github.com/user-attachments/assets/7c047938-77c5-465c-a65d-b7c788916d60)

![image](https://github.com/user-attachments/assets/9ecc7163-3ff5-40f2-a548-0e0c23ed302a)

Attack completed, now to detect this attack in splunk

![image](https://github.com/user-attachments/assets/b050cb4a-ff4c-416c-9a58-2bbed672b62b)

With the redirect field we can see to which site the redirection happened

![image](https://github.com/user-attachments/assets/732c0c91-675b-4f6b-a2c8-8decaadfbc09)

Also with the status code 302 we can understand that the redirection happened succesfully

![image](https://github.com/user-attachments/assets/58e5ea46-b707-4c96-a917-85cc567530dd)

with this splunk command we can see a list of attacker ips and the url to which redirection happened and the status code

```
index=* sourcetype=access_combined status=302
| regex uri_query="redirect*"| table _time, clientip, uri, status, useragent
| sort -_time
```
![image](https://github.com/user-attachments/assets/e99b4638-3367-435e-95ec-45ee9f846672)
