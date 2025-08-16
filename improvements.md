# Project Improvements Log

This document tracks major improvements and upgrades made to the DokuWiki Homelab project.


## 1. HTTPS implementation for local network

### 1.1. Summary

Configured HTTPS access for the DokuWiki instance running on the local network.  
A self-signed SSL certificate was generated, and Apache was configured to serve the site over HTTPS.  
This improves the security of data transferred between the client and the server, even within a trusted network.

### 1.2. Key steps

1. Enabled Port 443 with ufw
2. Generated a self-signed SSL certificate using OpenSSL
3. Created an Apache `<VirtualHost *:443>` configuration with SSL directives
4. Enabled SSL apache2 module
5. Restarted apache2 
6. Verified the HTTPS connection using a web browser

### 1.3. Reference

See [webserver.md – HTTPS configuration](webserver.md#i-https-configuration)


## 2. Strenghtening administrator password with password manager

### 2.1. Summary

Secured the Dokuwiki administrator account with a strong, random password. I used Bitwarden to generate and store a password.

### 2.2. Key Steps

1. Signed in ```bitwarden.com``` and downloaded its browser extension
2. Generated a strong, random password
3. Logged in to Dokuwiki's Admin account
4. Changed the old password to the new one
5. Saved the new password in password manager
6. Verified the password by logging out and back in

### 2.3. Reference

See [dokuwiki_config.md - Strengthen adminintrator password](dokuwiki_config.md#i-strengthen-administrator-password)


## 3. Automated backup for DokuWiki

### 3. 1. Summary

Ensured that the DokuWiki system and its configuration are backed up regularly without manual intervention, to protect against accidental data loss or corruption.

### 3.2. Key steps

1. Identified backup scope
   - DokuWiki content directory: `/var/www/html/dokuwiki`
   - Apache configuration: `/etc/apache2/sites-available/dokuwiki.conf`
   - SSL certificates (for HTTPS): `/etc/ssl/`
2. Created backup script and stored it in `/backup/dokuwiki/`
3. Automated via Cron to run the script every day at midnight  
4. Manually executed the script to confirm that backup works
5. Verified archive contents and restore procedure

### 3.3. Reference

See [dokuwiki_config.md - Automated backup for DokuWiki](dokuwiki_config.md#ii-automated-backup-for-dokuwiki)

