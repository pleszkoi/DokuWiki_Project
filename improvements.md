# Project Improvements Log

This document tracks major improvements and upgrades made to the DokuWiki Homelab project

## 1. HTTPS Implementation for Local Network

### 1.1. Summary
Configured HTTPS access for the DokuWiki instance running on the local network.  
A self-signed SSL certificate was generated, and Apache was configured to serve the site over HTTPS.  
This improves the security of data transferred between the client and the server, even within a trusted network.

### 1.2. Key steps:
1. Enabled Port 443 with ufw.
2. Generated a self-signed SSL certificate using OpenSSL.
3. Created an Apache `<VirtualHost *:443>` configuration with SSL directives.
4. Enabled SSL apache2 module
5. Restarted apache2 
6. Verified the HTTPS connection using a web browser.

### 1.3. Reference:
See [webserver.md – HTTPS Configuration](webserver.md#I.-HTTPS-Configuration)
