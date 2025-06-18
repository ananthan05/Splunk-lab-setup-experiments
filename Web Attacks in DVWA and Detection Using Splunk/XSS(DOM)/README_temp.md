DOM-based XSS occurs when malicious input is handled insecurely by client-side JavaScript and injected into the page without proper sanitization.

First we will try attacking with payload `<script>alert(1)</script>`

![Screenshot 2025-06-18 155222](https://github.com/user-attachments/assets/097b899a-7cfd-43dd-9a4d-350a7b1e9b7a)

After giving enter no alert was triggered, this is likely due to the input sanitization of the site as it avoids taking `<script>` as input

![Screenshot 2025-06-18 155358](https://github.com/user-attachments/assets/abbce36d-5e3a-4d64-8374-ddccf1f899cc)

We will try giving another payload.

![image](https://github.com/user-attachments/assets/7cb50328-f807-4107-b3b2-247c42b25239)

For the payload `<img src=x onerror=alert(1)` payload to work we need to break out of the <select> field.
