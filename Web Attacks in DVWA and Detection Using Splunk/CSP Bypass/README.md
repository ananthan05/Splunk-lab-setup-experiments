CSP (Content Security Policy) bypass is a technique used to circumvent browser-enforced security rules that restrict loading or executing untrusted content like scripts.

First we will try to bypass csp with a payload <script>alert(1)</script>

![image](https://github.com/user-attachments/assets/efee85f7-4862-43af-81bb-97727a960b5e)

Nothing will happen after entering the payload as csp blocks the attack because it lacks the nonce, A nonce in Content Security Policy (CSP) acts as a one-time, cryptographically random token that’s embedded both in the CSP header and in the inline script tag.

Now we will add the nonce value from the site into the payload and check if it accepts the payload

`<script nonce="TmV2ZXIgZ29pbmcgdG8gZ2l2ZSB5b3UgdXA=">alert(1)</script>` 

Now when we click enter the alert message will pop up 

![image](https://github.com/user-attachments/assets/06852ae7-6ff3-4864-99fe-a636963dd906)

Only scripts with the valid nonce are allowed to run. So even if an attacker injects a <script>, it won't execute unless they know the exact nonce, which changes on every request making XSS much harder to pull off.

Now to detect it through splunk

Before detection part we need to add the php post value into a log that will be forwarded to splunk,only then we can detect the payload

![image](https://github.com/user-attachments/assets/dfe212e8-4d07-49dc-968c-8690b7bc98b7)

Now we can serch for common xss attack payload terms 

![image](https://github.com/user-attachments/assets/515c2876-a4ad-462d-9d34-768ff7c8d567)

In the attckers log we can also see that nonce is also added into the paylaod by the attacker so we can be sure that csp bypass attack was tried here

A Content-Security-Policy header allows script execution only if the nonce is valid.The script would be blocked otherwise.
