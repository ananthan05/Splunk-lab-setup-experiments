DOM-based XSS occurs when malicious input is handled insecurely by client-side JavaScript and injected into the page without proper sanitization.

First we will try attacking with payload `<script>alert(1)</script>`

![Screenshot 2025-06-18 155222](https://github.com/user-attachments/assets/097b899a-7cfd-43dd-9a4d-350a7b1e9b7a)

After giving enter no alert was triggered, this is likely due to the input sanitization of the site as it avoids taking `<script>` as input

![Screenshot 2025-06-18 155358](https://github.com/user-attachments/assets/abbce36d-5e3a-4d64-8374-ddccf1f899cc)

We will try giving another payload

![image](https://github.com/user-attachments/assets/7cb50328-f807-4107-b3b2-247c42b25239)

For the payload `<img src=x onerror=alert(1)` payload to work we need to break out of the <select> field

so by giving `</select><img src=x onerror=alert(1)` we can successfully trigger the alert

![image](https://github.com/user-attachments/assets/c539dff0-12c7-4881-ac0d-01329719cac4)

![image](https://github.com/user-attachments/assets/d02924a1-cb8e-4337-9398-6565aa6b4b7e)

Attack successful 

Now monitoring in splunk

By giving search command in splunk for the common xss releated script commands we can identify it was an xss attack

`search "<img" OR "onerror" OR "alert(1)" OR "<script"`

![image](https://github.com/user-attachments/assets/daee7b5f-a1ce-43f7-bfae-8c589c1e2992)

The request are all http GET request and add_event_listner.js is also present

![image](https://github.com/user-attachments/assets/592aaf5d-a3b2-4f3c-907f-38dbd5c1ec81)

The presence of add_event_listeners.js confirms DOM XSS because it contains the client-side JavaScript that reads user input from the URL and injects it into the page, enabling script execution only in the browser, not from the server.d 
