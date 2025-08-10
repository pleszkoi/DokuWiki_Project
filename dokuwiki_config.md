# DokuWiki config

This document describes the installation and configuration of DokuWiki. The goal is to create an internal documentation system in homelab environment.

- Server type: Virtual Machine (VirtualBox)
- Web Server: Apache 2
- PHP Version: 8.1.2
- DokuWiki Version: 2025-05-14a "Librarian"
- Access Path: /var/www/html/dokuwiki


## Installation and default settings

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


## Users and permissions

### User management

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

### Group management

A user can be a member of multiple groups, separated by commas:
```bash
liza:$1$...:Liza Szabó:liza@example.com:admin,user
```
Gruops do not have a separate file - they exist implicitly if there is a user accigsed to them.

### Permission levels

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


## Template management

### Default template

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

### Choose template

The template selection is done in the ```conf/local.php``` file. For example:
```php
$conf['template'] = 'starter';
```

### Customize template

Most templates contain HTML, CSS, and JavaScript files that can be modified as desired. Example path:
```bash
/var/www/html/dokuwiki/lib/tpl/starter/style.ini
```


## Installing and managing plugins

DokuWiki's functionality can be extended with plugins. These are community-developed extensions that add new features to the core system, such as:
- WYSIWYG editor
- Diagram generation
- Permission management improvement
- Search, tags, forms, etc.

### Installing plugins

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

### Check plugins

In the web interface, the Extension Manager list contains active extension.
In case of any error, check DokuWiki logfile:
```bash
tail -f /var/www/html/dokuwiki/data/log/error.log
```

### Plugin settings

Many plugins have several configuration option, which are available under Admin/Configuration Settings/Plugin tab.
For example:
The ```wrap``` plugin allows to place different colored boxes, warnings and text formatting within a wiki page.
Usage example:
```txt
<WRAP info>
Ez egy információs doboz.
</WRAP>
```

### Remove plugins

- Log in to the Admin/Extension Manager menu. Search for the desired plugin and click to the Remove button.

- Manual deleting:
```bash
sudo rm -rf /var/www/html/dokuwiki/lib/plugins/wrap
```
