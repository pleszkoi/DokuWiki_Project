# Web Server Configuration

Documentation on installing and configuring DokuWiki web server using Apache on Ubuntu-based Virtual Machine.


## 1. Installing apache

### 1.1. Commands

```bash
sudo apt update
sudo apt upgrade
sudo apt install apache2
```

### 1.2. Checking

```bash
systemctl status apache2
```


## 2. Installing PHP

### 2.1. Command

```bash
sudo apt install php libapache2-mod-php php-xml php-gd php-json php-mbstring
```

### 2.2. Checking

```bash
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
```


## 3. Download and install DokuWiki

### 3.1. Download

```bash
wget https://download.dokuwiki.org/arc/dokuwiki/dokuwiki-stable.tgz
```

### 3.2. Unpack and move

```bash
tar -xvzf dokuwiki-stable.tgz
sudo mv dokuwiki-* /var/www/html/dokuwiki
```


## 4. Setting permissions

```bash
sudo chown -R www-data:www-data /var/www/html/dokuwiki
sudo chmod 755 /var/www/html/dokuwiki
```


## 5. Setting virtual host

### 5.1. Create file

```bash
sudo nano /etc/apache2/sites-available/dokuwiki.conf
```

### 5.2. Content

```apache
<VirtualHost *:80>
    ServerName dokuwiki.local
    DocumentRoot /var/www/html/dokuwiki

    <Directory /var/www/html/dokuwiki>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

## 6. Setting host name on host machine

On Ubuntu Linux: ```/etc/hosts```

```bash
192.168.1.88 dokuwiki.local
```


## 7. Restarting apache and activate site

```bash
sudo a2ensite dokuwiki.conf
sudo a2enmod rewrite
sudo systemctl reload apache2
```


## 8. Accessing the installer

In browser:
  ```arduino
  http://dokuwiki.local/install.php
  ```


## I. HTTPS configuration

This section describes how HTTPS was enabled for the local DokuWiki installation using a self-signed SSL certificate.

### I. 1. Preparation

- I checked if DokuWiki is enable with ```http://dokuwiki.local```.
- I enabled Port 443 with ```sudo ufw allow 443/tcp```.

### I. 2. Generate a self-signed SSL certificate

I ran the following command on the server:

```bash
sudo openssl req -x509 -nodes -days 365 \
  -newkey rsa:2048 \
  -keyout /etc/ssl/private/dokuwiki-selfsigned.key \
  -out /etc/ssl/certs/dokuwiki-selfsigned.crt
```

### I. 3. Create HTTPS VirtualHost configuration

- I opened the ```/etc/apache2/sites-available/dokuwiki.conf``` file and
- I modified it to:
``` apache
<VirtualHost *:80>
    ServerName dokuwiki.local
    Redirect / https://dokuwiki.local
</VirtualHost>

<VirtualHost *:443>
    ServerName dokuwiki.local
    DocumentRoot /var/www/html/dokuwiki
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/dokuwiki-selfsigned.crt
    SSLCertificateKeyFile /etc/ssl/private/dokuwiki-selfsigned.key

    <Directory /var/www/html/dokuwiki>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

### I. 4. Enable Apache SSL module

```bash
sudo a2enmod ssl
```

### I. 5. Restart Apache

```bash
sudo systemctl restart apache2
```

### I. 6. Test the connection

```arduino
https://dokuwiki.local
```


## II. Protection against brute-force attacks

Fail2Ban monitors logs and automatically bans the IP address upon repeated failed attempts.

### II. 1. Installing Fail2Ban

```bash
sudo apt install fail2ban -y
```

### II. 2. Creating basic configuration

```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

### II. 3. Setting Apache security

```ini
[apache-auth]
enabled = true
port    = http,https
logpath = /var/log/apache*/*error.log
maxretry = 5
bantime = 3600
```

### II. 4. Enabling Fail2Ban

```bash
sudo systemctl enable fail2ban
```

### II. 5. Starting Fail2Ban

```bash
sudo systemctl start fail2ban
```

### II. 6. Checking Fail2Ban status

```bash
sudo systemctl status fail2ban.service
sudo fail2ban-client status
sudo fail2ban-client status apache-auth
```
