![image](https://github.com/user-attachments/assets/a25dac50-fdbe-4c09-99ef-1e884b092c8f)## Launching an Attack from Another PC in the LAN:

Objective:To forge a valid session token with elevated privileges (e.g., admin access) by exploiting the deterministic nature of AES-128-ECB, where blocks are encrypted independently and can be rearranged without affecting decryption.

![image](https://github.com/user-attachments/assets/bed42e86-62c8-4bd0-959d-aa01a6ba9e23).


### Understanding the Vulnerability-

We now have **three tokens**:
- One for the **admin**
- One for a **valid user**
- One for a **session-expired user**

Our goal is to **log in as the admin** by analyzing and manipulating these tokens.

We aim to:
1. Extract the **valid session ID** from the valid user's token.
2. Extract **admin privileges** from the admin’s token.
3. Replace corresponding blocks in the expired token with the above.

By doing this, we can forge a **new token** that has:
- A valid session
- Admin-level access

###  🧪 Methodology

This works because the tokens are encrypted using **AES-128-ECB**, which encrypts each 16-byte block independently. That means:
- Identical plaintext blocks = identical ciphertext blocks.
- Blocks can be swapped between tokens encrypted with the same key.

### Token Swapping Steps:

1. **Identify blocks** in each token by splitting them into 16-byte (32 hex char) chunks.
2. Determine which blocks represent:
   - Session ID
   - Role or privileges
3. Swap relevant blocks into the expired token.
4. Use the forged token to log in as admin.

## 🧰 Prerequisite

To make the token analysis easier, install **Sublime Text**:

### Installation (Linux)

```
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```
![image](https://github.com/user-attachments/assets/f0a4fcd6-bb85-4e3e-8142-510d6aeec4da)

```
curl -fsSL https://download.sublimetext.com/sublimehq-pub.gpg | sudo gpg --dearmor -o /usr/share/keyrings/sublimehq-archive.gpg
```

```
echo "deb [signed-by=/usr/share/keyrings/sublimehq-archive.gpg] https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
```

![image](https://github.com/user-attachments/assets/206fbc70-54ad-41cf-a900-8586a549a9a8)


```
sudo apt update
sudo apt install sublime-text
```

![image](https://github.com/user-attachments/assets/bbc6d33e-4b78-436b-8348-4393201bed19)

Now we anlyis the tokens from the **Sublime Text**. 

open it using the command 

```
subl
```
Then paste the three token there.

![image](https://github.com/user-attachments/assets/87fb7504-9e4e-47bf-9263-aab5b3bec9da)

### Token Splitting Using Sublime Text (AES Block Viewer)

To analyze AES-128-ECB encrypted tokens, it's useful to break the ciphertext into **32-character chunks** (each representing a 16-byte AES block). Here’s how to do that using **Sublime Text**:

---

###  1. Open the Replace Panel
- Navigate to `Find → Replace...`  
- Or use the shortcut: `Ctrl + H` 

---

###  2. Enable Regex Mode
- Look at the bottom left of the Find/Replace panel.
- Click the `.*` button to turn on **Regular Expression Mode**.

> It should turn **orange** when active.

---

### 🧠 3. Use These Fields:

**Find:**  
```regex
([0-9a-fA-F]{32})
```
This matches exactly 32 hexadecimal characters – one full AES block.

![image](https://github.com/user-attachments/assets/a4094748-cfcd-43d5-9f2f-04a8e871ad9c)

Replace:

```regex
\1\n
```

This keeps the match and adds a newline after every 32-character block.

and press replace all button.

![image](https://github.com/user-attachments/assets/3c4ac75c-667e-4bcc-8ff6-0cd9fc08e688)

 by anylysing we undsertood .

![image](https://github.com/user-attachments/assets/e6afa01e-160b-4c4b-ad97-c2cfeb92c123)


###  Block Index Mapping

| Block Index | Description       | Notes                            |
|-------------|-------------------|----------------------------------|
| `0`         | **Username**       | First block of each token        |
| `1`         | **Expiry**         | Usually reused among tokens      |
| `2`         | **Privilege level**| User/Admin privileges             |
| `3+`        | **Bio/Extra**      | Arbitrary or user-controlled data |

---

![image](https://github.com/user-attachments/assets/98caf8dc-36b4-4d42-a2ac-c508dad18d72)

From my analysis, I understood that I need to change Sweep's session token to match Soo's, because Soo has a valid session token. Since Sweep and the admin share the same session structure, replacing Sweep's expired session block with Soo's valid one allows me to craft a valid session token with admin privileges


![image](https://github.com/user-attachments/assets/b8ea4203-a4c7-4154-a73a-13594c2c0079)

fianl token.

![image](https://github.com/user-attachments/assets/701b5f06-f54a-4331-bfad-f34407519e14)

```
3061837c4f9debaf19d4539bfa0074c1
174d4b2659239bbc50646e14a70becef
837d1e6b16bfae07b776feb7afe57630
caeb574f10f349ed839fbfd223903368 
873580b2e3e494ace1e9e8035f0e7e07
```
now change it orginal format and send the token.for crt+h thrn type `\n` and and press replace all.

![image](https://github.com/user-attachments/assets/a67ae001-59b7-49b0-9b16-49427014e3ee)

```
3061837c4f9debaf19d4539bfa0074c1174d4b2659239bbc50646e14a70becef837d1e6b16bfae07b776feb7afe57630caeb574f10f349ed839fbfd223903368873580b2e3e494ace1e9e8035f0e7e07
```

![image](https://github.com/user-attachments/assets/1daad3ff-9fdd-414f-946a-a9b13855c7a7)

 Attack Successful


