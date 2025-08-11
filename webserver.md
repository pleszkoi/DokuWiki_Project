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
