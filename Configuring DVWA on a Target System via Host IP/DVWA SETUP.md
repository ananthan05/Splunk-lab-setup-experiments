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

### 3️ Set Permissions

```
sudo chown -R www-data:www-data /var/www/html/DVWA/
sudo chmod -R 755 /var/www/html/DVWA/
```

### 4️ Configure MariaDB

```
sudo service mysql start
sudo mysql -u root
```

```sql
CREATE DATABASE dvwa;
CREATE USER 'dvwauser'@'localhost' IDENTIFIED BY 'dvwapass';
GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwauser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```


### 5️ Configure DVWA

```
cd /var/www/html/DVWA/config
sudo cp config.inc.php.dist config.inc.php
sudo nano config.inc.php
```

$_DVWA['db_user'] = 'dvwauser';
$_DVWA['db_password'] = 'dvwapass';


### 6️ Restart Apache

```
sudo systemctl restart apache2
```

### 7️ Access DVWA

Open browser and go to:

  `http://localhost/DVWA/`

Or from LAN:

 ` http://<host-ip-address>/DVWA/`

Login with:

Username: admin
Password: password

Then click "Create / Reset Database".
