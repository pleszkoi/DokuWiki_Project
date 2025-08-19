# Error Detection and Troubleshooting

This document contains the errors encountered during homelab project, their causes and the troubleshooting steps.


## 1. The firewall blocked webserver

### 1.1. Error description

The DokuWiki server was started, the server (Apache) was running, but the http://dokuwiki.local or the server IP address (http://192.168.1.88) was unrechable from the browser.

Steps checked during troubleshooting:
- The server service was running (```systemctl status apache2```).
- Port 80 was active (```ss -tuln | grep 80```).
- Hosts file was properly configured.
- The VM had an IP address and it was reachable by pinging.

### 1.2. Reason

The firewall was active on the server, but only the SSH (Port 22) was allowed, the HTTP (Port 80) was not.

### 1.3. Solution

Allowing Port 80:

```bash
sudo ufw allow 80/tcp
sudo ufw reload
```

Checking firewall:

```bash
sudo ufw status
```

Checking reachability:

```bash
http://192.168.1.88
http://dokuwiki.local
```

Accessing the server IP address and dokuwki.local from the browser was successful.


## 2. IPv6 preference caused ping problem

### 2.1. Error description

I haven't got any response after running `ping google.com` command. I only saw an IPv6 address:
`PING google.com(bud02s39-in-x0e.1e100.net(2a00:1470:500d:80e::200d)) 56 data bytes`
The process stopped and the data loss was 100%.

### 2.2. Reason

The system primarily used IPv6 addresses for name resolution, but there was no IPv6 internet connection in the environment.

### 2.3. Solution

Disabling IPv6 in the system:

```bash
sudo nano /etc/sysctl.conf
```

Added lines:

```bash
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
```

Applying configuration:

```bash
sudo sysctl -p
```

Checking:

```bash
ping google.com
```
The ping uses IPv4 addresses and it runs.


## 3. SSHD Jail did not work under Fail2Ban

### 3.1. Error description

After installing `fail2ban`, testing the status of the service with `sudo systemctl status fail2ban.service`, the output showed that `fail2ban` does not work.
```txt
...ERROR   Failed during configuration: Have not found any log file for sshd jail...
```

### 3.2. Reason

The `sshd jail` did not work properly. The settings of `[sshd]` section in `jail.local` file showed that the jail was using dropbear's log settings: 
```bash
[sshd]
port = ssh
logpath = %(dropbear_log)s
backend = %(dropbear_backend)s
```
It resulted that the system did not find the correct log file. Furthermore, the `/var/log/auth.log` file did not exist either, so the failed login attempts were not processed either.

### 3.3. Solution

Rewriting the sshd jail to use the systemd backend and the sshd.service unit:
```ini
[sshd]
enabled = true
port    = ssh
logpath = /var/log/auth.log
backend = systemd
```

Reloading all service files and update its internal configuration:
```bash
sudo systemctl daemon-reload
```

Restarting fail2ban:
```bash
sudo systemctl restart fail2ban
```

Checking fail2ban status:
```bash
sudo systemctl status fail2ban
```
After specifying the systemd backend and the appropriate sshd log source, fail2ban became functional and was able to ban failed login attempts.
