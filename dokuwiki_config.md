# DokuWiki config

This document describes the installation and configuration of DokuWiki. The goal is to create an internal documentation system in homelab environment.

- Server type: Virtual Machine (VirtualBox)
- Web Server: Apache 2
- PHP Version: 8.1.2
- DokuWiki Version: 2025-05-14a "Librarian"
- Access Path: /var/www/html/dokuwiki


## 1. Installation and default settings

### 1.1. Downloading and unpacking

```bash
cd /var/www/html/
sudo wget https://download.dokuwiki.org/src/dokuwiki/dokuwiki-stable.tgz
sudo tar -xvzf dokuwiki-stable.tgz
sudo mv dokuwiki-* dokuwiki
```

### 1.2. Setting permissions

```bash
sudo chown -R www-data:www-data /var/www/html/dokuwiki
```

### 1.3. Restarting webserver

```bash
sudo systemctl restart apache2
```

### 1.4. Accessing the installer

```bash
http://dokuwiki.local/install.php
```

### 1.5. Post-installation tasks

Deleting install.php
```bash
sudo rm /var/www/html/dokuwiki/install.php
```


## 2. Users and permissions

### 2.1. User management

DokuWiki has its own built-in user- and permission management system that does not require an external database.

- **Add users**

  Log in to the website as administrator and enter the values for the new user in the User Manager menu. Finally click the Create User button.

- **Store users**

  User data is stored in the following file:
  ```bash
  /var/www/html/dokuwiki/conf/users.auth.php
  ```
  This is a simple file, delimited by ```:```, for example:
  ```bash
  admin:$1$6vkd...:Adminisztrátor:admin@example.com:admin
  peter:$1$1s2a...:Péter Kovács:peter@example.com:user
  ```

### 2.2. Group management

A user can be a member of multiple groups, separated by commas:
```bash
liza:$1$...:Liza Szabó:liza@example.com:admin,user
```
Groups do not have a separate file - they exist implicitly if there is a user assigned to them.

### 2.3. Permission levels

Access permissions can be set per page, namespace or globally.

- **Permission levels**

| Level       | Code | Description                    |
|-------------|------|--------------------------------|
| None        | 0    | No access                      |
| Read        | 1    | Read                           |
| Write       | 2    | Page editing                   |
| Upload      | 4    | Uploading files                |
| Full access | 8    | Deleting page, admin functions |

- **Setting permissions**

Log in as administrator. Open the Access Control List Management menu and enter the desired namespace or page. Assign permission levels to the users or groups.

The configuration is stored in the following file:
```bash
/var/www/html/dokuwiki/conf/acl.auth.php
```


## 3. Template management

### 3.1. Default template

The default template is ```dokuwiki```.
It's access path is ```/var/www/html/dokuwiki/lib/tpl/dokuwiki/```.

### 3.2. Installing new template

- Log in as administrator. On the Admin/Extension Manager menu, choose Search and Install option. Search for the desired template and click on the Install button.

- Manual installing
  ```bash
  cd /var/www/html/dokuwiki/lib/tpl/
  wget https://download.dokuwiki.org/template:starter -O starter.zip
  unzip starter.zip
  ```

### 3.3. Choose template

The template selection is done in the ```conf/local.php``` file. For example:
```php
$conf['template'] = 'starter';
```

### 3.4. Customize template

Most templates contain HTML, CSS, and JavaScript files that can be modified as desired. Example path:
```bash
/var/www/html/dokuwiki/lib/tpl/starter/style.ini
```


## 4. Installing and managing plugins

DokuWiki's functionality can be extended with plugins. These are community-developed extensions that add new features to the core system, such as:
- WYSIWYG editor
- Diagram generation
- Permission management improvement
- Search, tags, forms, etc.

### 4.1. Installing plugins

- Log in as administartor, On the Admin/Extension Manager menu, choose Search and Install option. Search for the desired plugin and click on the Install button.

- Manual installing
    - Go to the plugin folder
      ```bash
      cd /var/www/html/dokuwiki/lib/plugins/
      ```
    
    - Download the plugin package
      ```bash
      wget https://github.com/splitbrain/dokuwiki-plugin-wrap/archive/master.zip -O wrap.zip
      unzip wrap.zip
      mv dokuwiki-plugin-wrap-master wrap
      ```

    - Set permissions
      ```bash
      sudo chown -R www-data:www-data wrap
      ```

### 4.2. Check plugins

In the web interface, the Extension Manager list contains active extension.
In case of any error, check DokuWiki logfile:
```bash
tail -f /var/www/html/dokuwiki/data/log/error.log
```

### 4.3. Plugin settings

Many plugins have several configuration option, which are available under Admin/Configuration Settings/Plugin tab.
For example:
The ```wrap``` plugin allows to place different colored boxes, warnings and text formatting within a wiki page.
Usage example:
```txt
<WRAP info>
Ez egy információs doboz.
</WRAP>
```

### 4.4. Remove plugins

- Log in to the Admin/Extension Manager menu. Search for the desired plugin and click to the Remove button.

- Manual deleting:
  ```bash
  sudo rm -rf /var/www/html/dokuwiki/lib/plugins/wrap
  ```


## 5. Content saving and backup

In DokuWiki saving is simple because there is no need for a database. All content is stored in files. 

### 5.1. Content to save

- **Data** - wiki's pages, files, uploaded images
  ```swift
  /var/www/html/dokuwiki/data/
  ```

- **Configuration** - settings
  ```swift
  /var/www/html/dokuwiki/conf/
  ```

- **Templates (skins)** - if a custom template has been installed or modified
  ```swift
  /var/www/html/dokuwiki/lib/tpl/
  ```

- **Extensions (plugins)** - Installed or customized plugins
  ```swift
  /var/www/html/dokuwiki/lib/plugins/
  ```

### 5.2. Creating backup

- **Create full backup to compressed archive**
  ```bash
  sudo tar -czvf dokuwiki_backup_$(date +%F).tar.gz \
      /var/www/html/dokuwiki/data \
      /var/www/html/dokuwiki/conf \
      /var/www/html/dokuwiki/lib/tpl \
      /var/www/html/dokuwiki/lib/plugins
  ```

- **Automatic daily backup (with cron)**
    - Opening crontab editor
      ```bash
      crontab -e
      ```
    - Adding the following line (e.g.: creating backup every day at 3 AM)
      ```bash
      0 3 * * * tar -czf /backup/dokuwiki_$(date +\%F).tar.gz /var/www/html/dokuwiki/{data,conf,lib/tpl,lib/plugins}
      ```

### 5.3. Restore from backup

- Unpacking compressed backup
  ```bash
  sudo tar -xzvf dokuwiki_backup_YYYY-MM-DD.tar.gz -C /
  ```

- Restoring permissions
  ```bash
  sudo chown -R www-data:www-data /var/www/html/dokuwiki
  ```


## 6. Security settings

The following settings help reduce the risk of unauthorized access, data leakage or vandalism.

### 6.1. Admin password and user access

- Using strong admin password
- Keeping necessary number of admin users
- Deleting or bolcking inactive user accounts

### 6.2. Using HTTPS

The encrypted connection ensures that passwords and data cannot be intercepted.
Apache configuration example:
```apache
<VirtualHost *:443>
    ServerName wiki.pelda.local
    DocumentRoot /var/www/html/dokuwiki

    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/dokuwiki.crt
    SSLCertificateKeyFile /etc/ssl/private/dokuwiki.key

    <Directory /var/www/html/dokuwiki>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

### 6.3. Denying direct file access

Example of protecting the ```data/``` folder:
```apache
# /var/www/html/dokuwiki/data/.htaccess
Deny from all
```

### 6.4. Restricting admin interface based on IP address

Making the administration interface accessible only from specific IP addresses.
For example:
```apache
<Directory /var/www/html/dokuwiki>
    <FilesMatch "(admin|install)\.php">
        Require ip 192.168.1.88
    </FilesMatch>
</Directory>
```

### 6.5. Automatic logout after inactivity

It can be set in Admin/Configutartion Settings/auth_security_timeout. 

### 6.6. Regular updates

- Updating Dokuwiki from Admin interface.
- Updating extensions and templates from Admin interface
- Updating the system (Linux. Apache, PHP):
  ```bash
  sudo apt update && sudo apt upgrade -y
  ```

### 6.7. Logging and monitoring

It is worth reviewing these logs regularly for suspicious activity:

- DokuWiki logs: In ```data/log/``` folder.
- Apache logs:
    - Access logs: ```/var/log/apache2/access.log```
    - Error logs: ```/var/log/apache2/error.log```
