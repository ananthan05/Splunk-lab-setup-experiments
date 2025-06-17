Initialy we will try to enter the payload into the url after entering a random name into the site

![image](https://github.com/user-attachments/assets/6abbaeeb-ac36-45d6-ac48-14c58ded359f)

now we will replace the value after name field with our exploit
`<script>alert(1)</script>`


![Screenshot 2025-06-17 162129](https://github.com/user-attachments/assets/d30467f0-f942-40a0-8df5-f54cc450a0c5)

But after execution we can see that popup alert didnt happen, thats becase the site has input sanitization and replaces the word `<script>`

![Screenshot 2025-06-17 160753](https://github.com/user-attachments/assets/1a3ff18d-f25e-4db3-b396-a29dedd208cc)

now we will change the case of the script value

`<Script>alert(1)</Script>`

![Screenshot 2025-06-17 162236](https://github.com/user-attachments/assets/3c2cef23-14c3-4794-90b1-1aab1db5670b)

after enetering modified script we could see the popup alert

Now to monitor the attack

we can use the command `regex uri_query="(<script|onerror|alert|%3C|%3E)"` in splunk it will look for common xss keyword

![image](https://github.com/user-attachments/assets/82557876-cb7f-49b2-99c9-be1c58d32b09)

we can use the commnad `host="victim" sourcetype=access_combined | regex uri_query="(<script|onerror|alert|%3C|%3E)"   | stats  count by uri_query, clientip, _time` to list the attck happened as table and include uri_query with client ip and timestamp

![Screenshot 2025-06-17 163630](https://github.com/user-attachments/assets/37793379-1b3b-4b7a-94b3-8b42f33cf740)


