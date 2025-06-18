Stored XSS occurs when a malicious script is permanently saved on the server (e.g., in a database) and executed in users' browsers when they view the affected content.

We will try entering the payload `<script>alert(1)</script>` in the message field

![image](https://github.com/user-attachments/assets/9e7c82c1-791e-4ac3-8c4d-a609f2e644c6)

After clicking sign guestbook no pop up alert was triggered

![image](https://github.com/user-attachments/assets/27e13dbb-e921-4340-8e11-0508d0132225)

The site sanitizes the input values so we have to change the payload

![image](https://github.com/user-attachments/assets/6e0e25a5-cdcd-4e16-9fa9-3322856374a1)

We will try with the payoad `<Script>alert(1)</Script>`

![image](https://github.com/user-attachments/assets/673055ca-5be7-4148-bebe-1c65c3d2293e)

No alert was triggedred here also, we will try placing alert in the name field

For that we need to chnage the maxlength of name field from 10 to 50 through the inspect option in browser

![Screenshot 2025-06-18 102819](https://github.com/user-attachments/assets/0820732f-eaaa-4cbd-a95d-5bd6fa26974f)

Now when clicked on sign guestbook button alert will be triggered

![Screenshot 2025-06-18 102935](https://github.com/user-attachments/assets/dfde014c-ddf3-4384-a1ad-1f1c24aa6b05)

Attack successfull, now to detect it in splunk
