# DokuWiki Config

This document describes the installation and configuration of DokuWiki. The goal is to create an internal documentation system in homelab environment.

- Server type: Virtual Machine (VirtualBox)
- Web Server: Apache 2
- PHP Version: 8.1.2
- DokuWiki Version: 2025-05-14a "Librarian"
- Access Path: /var/www/html/dokuwiki


## Installation and Default Settings

- Downloading and unpacking
```bash
cd /var/www/html/
sudo wget https://download.dokuwiki.org/src/dokuwiki/dokuwiki-stable.tgz
sudo tar -xvzf dokuwiki-stable.tgz
sudo mv dokuwiki-* dokuwiki
```

- Setting permissions
```bash
sudo chown -R www-data:www-data /var/www/html/dokuwiki
```

- Restarting webserver
```bash
sudo systemctl restart apache2
```

- Accessing the installer
```bash
http://dokuwiki.local/install.php
```

- Post-installation tasks
Deleting install.php
```bash
sudo rm /var/www/html/dokuwiki/install.php
```


## Users and Permissions

### User Management

DokuWiki has its own built-in user- and permission management system that does not require an external database.

- Add users
Log in to the website as administrator and enter the values for the new user in the User Manager menu. Finally click the Creat User button.

- Store users
User data is stored in the following file:
```bash
/var/www/html/dokuwiki/conf/users.auth.php
```
This is a simple file, delimited by ```:```, for example:
```bash
admin:$1$6vkd...:Adminisztrátor:admin@example.com:admin
peter:$1$1s2a...:Péter Kovács:peter@example.com:user
```

### Group Management

A user can be a member of multiple groups, separated by commas:
```bash
liza:$1$...:Liza Szabó:liza@example.com:admin,user
```
Gruops do not have a separate file - they exist implicitly if there is a user accigsed to them.

### Permission Levels

Access permissions can be set per page, namespace or globally.

- Permission levels:

| Level       | Code | Description                    |
|-------------|------|--------------------------------|
| None        | 0    | No access                      |
| Read        | 1    | Read                           |
| Write       | 2    | Page editing                   |
| Upload      | 4    | Uploading files                |
| Full access | 8    | Deleting page, admin functions |

- Setting permissions

Log in as administrator. Open the Access Control List Management menu and enter the desired namespace or page. Assign permission levels to the users or groups.

The configuration is stored in the following file:
```bash
/var/www/html/dokuwiki/conf/acl.auth.php
```


## Template Management

### Default Template

The default template is ```dokuwiki```.
It's access path is ```/var/www/html/dokuwiki/lib/tpl/dokuwiki/```.

### Installing new template

- Log in as administrator. On the Admin/Extension Manager menu, choose Search and Install option. Search for the desired template and click on the Install button.

- Manual installing
```bash
cd /var/www/html/dokuwiki/lib/tpl/
wget https://download.dokuwiki.org/template:starter -O starter.zip
unzip starter.zip
```

### Choose Template

The template selection is done in the ```conf/local.php``` file. For example:
```php
$conf['template'] = 'starter';
```

### Customize template

Most templates contain HTML, CSS, and JavaScript files that can be modified as desired. Example path:
```bash
/var/www/html/dokuwiki/lib/tpl/starter/style.ini
```
