# DVWA Installation on Kali Linux with Apache2

This guide documents how to install Damn Vulnerable Web Application (DVWA) on Kali Linux using Apache2 and MariaDB.

## 📦 Requirements

- Kali Linux (tested on latest release)
- Apache2
- PHP
- MariaDB
- Git

## 🔧 Installation Steps

### 1. Install Required Packages
```bash
sudo apt update
sudo apt install apache2 php php-mysqli mariadb-server git -y
```
### 2️. Clone DVWA to Apache Web Directory

```
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git
```

Clones the DVWA GitHub repository into Apache’s root directory so it can be served via the web.

![image](https://github.com/user-attachments/assets/7e539e11-009f-4539-a354-e0d45320e9fd)


### 3️ Set Permissions

```
sudo chown -R www-data:www-data /var/www/html/DVWA/
sudo chmod -R 755 /var/www/html/DVWA/
```
chown: Changes ownership to the Apache user (www-data) for DVWA files.

chmod: Grants read/write/execute permissions to the owner and read/execute to others.

![image](https://github.com/user-attachments/assets/4de78b6d-3f2d-4dab-866b-122180686e4b)


### 4️ Configure MariaDB

```
sudo service mysql start
sudo mysql -u root
```

Then execute the following SQL commands to set up the DVWA database and user:

```sql
CREATE DATABASE dvwa;
CREATE USER 'dvwauser'@'localhost' IDENTIFIED BY 'dvwapass';
GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwauser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
CREATE DATABASE: Creates a new database named dvwa.

CREATE USER: Adds a user dvwauser with password dvwapass.

GRANT: Grants the user full access to the dvwa database.

FLUSH PRIVILEGES: Applies the changes.

![image](https://github.com/user-attachments/assets/4bcb3ad8-7018-4d7a-84e3-b2279441b234)


### 5️ Configure DVWA

```
cd /var/www/html/DVWA/config
sudo cp config.inc.php.dist config.inc.php
sudo nano config.inc.php
```
Update the following lines in config.inc.php:

$_DVWA['db_user'] = 'dvwauser';
$_DVWA['db_password'] = 'dvwapass';

![Screenshot 2025-06-13 105102](https://github.com/user-attachments/assets/59fb3c79-33ed-466b-990b-43c17b27687c)


### 6️ Restart Apache

```
sudo systemctl restart apache2
```
![image](https://github.com/user-attachments/assets/786b522a-32a9-4537-8b36-ea6224797732)

hostip 

![image](https://github.com/user-attachments/assets/0a048a42-cb85-4838-85fc-ee47186355c8)

### 7️ Access DVWA

Open browser and go to:

  `http://localhost/DVWA/`

Or from LAN:

 ` http://<host-ip-address>/DVWA/`

![pp](https://github.com/user-attachments/assets/00562279-8041-4e1f-852f-63426722136f)


Login with:

Username: admin
Password: password


Then click "Create / Reset Database".


