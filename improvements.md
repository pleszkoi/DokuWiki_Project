# Project Improvements Log

This document tracks major improvements and upgrades made to the DokuWiki Homelab project.


## 1. HTTPS Implementation for Local Network

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

1. Signed in Bitwarden and downloaded its browser extension
2. Generated a strong, random password
3. Logged in to Dokuwiki administrator account
4. Changed the old password to the new one
5. Saved the new password in password manager
6. Verified the password by logging out and in

### 2.3. Reference

See [dokuwiki_config.md - Strengthen adminintrator password](dokuwiki_config.md#i-strengthen-administrator-password)
