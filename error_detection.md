# Error Detection and Troubleshooting

This document contains the errors encountered during homelab project, their causes and the troubleshooting steps.


## 


## IPv6 preference caused ping problem

### Error description

I haven't got any response after running 'ping google.com' command. I only saw an IPv6 address:
PING ggogle.com(bud02s39-in-x0e.1e100.net(2a00:1470:500d:80e::200d)) 56 data bytes
The process stopped and the data loss was 100%.

### Reason

The system primarily used IPv6 addresses for name resolution, but there was no IPv6 internet connection in the environment.

### Solution

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

Examining:

```bash
ping google.com
```
The ping uses IPv4 addresses and it runs.


