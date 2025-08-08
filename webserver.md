# Web Server Configuration

Documentation on installing and configuring DokuWiki web server using Apache on Ubuntu-based Virtual Machine.


## Installing Apache

- Commands:
```bash
sudo apt update
sudo apt upgrade
sudo apt install apache2
```

-  Checking:
```bash
systemctl status apache2
```


## Installing PHP

- Command:
```bash
sudo apt install php libapache2-mod-php php-xml php-gd php-json php-mbstring
```

- Checking:
```bash
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
```


## Download and Install DokuWiki

- Download:
```bash
wget https://download.dokuwiki.org/arc/dokuwiki/dokuwiki-stable.tgz
```

- Unpack and move:
```bash
tar -xvzf dokuwiki-stable.tgz
sudo mv dokuwiki-* /var/www/html/dokuwiki
```


## Setting Permissions
```bash
sudo chown -R www-data:www-data /var/www/html/dokuwiki
sudo chmod 755 /var/www/html/dokuwiki
```


## Setting Virtual Host

- Create File:
```bash
sudo nano /etc/apache2/sites-available/dokuwiki.conf
```

- Content:
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

## Setting Host Name on Host Machine

On Ubuntu Linux: /etc/hosts

```
192.168.1.88 dokuwiki.local
```


## Restarting Apache and Activate Site

```bash
sudo a2ensite dokuwiki.conf
sudo a2enmod rewrite
sudo systemctl reload apache2
```


## Accessing the Installer

- In browser:
```
http://dokuwiki.local/install.php
```
