# Network Configuration

This document describes the homelab's network settings, the IP addressing, hostname conventions, and the DNS and port forwarding settings.


## 1. Network Topology

The figure below shows the network topology of the router, physical host and the virtual machine.

### 1.1. Diagram

![Network Topology](/images/Network_topology.png)

### 1.2. Description

- Host (Linux): 192.168.1.87
- Virtual Machine (DokuWiki server): 192.168.1.88
- Router: 192.168.1.1
- Connection: Bridged network → The VM is on the same network as the host machine.


## 2. IP Address and Hostname Table

The table below lists the devices in the homelab network, their IP address, hostname, role, network mode, MAC address and additional notes.

| Device         | IP Address   | Hostname       | Role                 | Network mode | MAC Address       | Notes                     |
|----------------|--------------|----------------|----------------------|--------------|-------------------|---------------------------|
| Router         | 192.168.1.1  | router.local   | Gateway/DHCP server  | -            | 3c:58:5d:e9:c0:56 | Providing internet access |
| Host machine   | 192.168.1.87 | host.local     | Hypervisor (Linux)   | -            | 30:f6:ef:d5:13:f5 | Runs the Virtual Machine  |
| Virual Machine | 192.168.1.88 | dokuwiki.local | Webserver (Dokuwiki) | Bridged      | 08:00:27:4c:b3:f3 | Apache + PHP installed    |


## 3. Hosts File Configuration

Using the 'hosts' file, I configured name resolution on the host machine so that DokuWiki can be accessed at 'http://dokuwiki.local'.

- The file's location: '/etc/hosts'
- Editing the file: 
  ```bash
  sudo vim /etc/hosts
  ```
- Entry: 
  ```bash
  192.168.1.88 dokuwiki.local
  ```


## 4. Ports and Services

The table below shows the main ports used by the system and their function.

| Port | Protocoll | Service       | Description                      |
|------|-----------|---------------|----------------------------------|
| 80   | TCP       | HTTP (Apache) | DokuWiki web interface           |
| 22   | TCP       | SSH           | Remote management for the server |

The opened ports can be checked with the following command:
```bash
sudo ss -tuln
```

Example output:
```markdown
tcp LISTEN 0 511 *:80 *:*
tcp LISTEN 0 128 *:22 *:*
```


## 5. Firewall Rules

I configured the firewall for network security to allow only the necessary ports.

Used UFW commands:

1. Enabled ports:
  ```bash
  sudo ufw allow 22/tcp
  sudo ufw allow 80/tcp
  ```

2. Enabling firewall:
  ```bash
  sudo enable ufw
  ```

3. Check rules
  ```bash
  sudo ufw status
  ```

Example output:
  ```
  Status: active
  
  To             Action   From
  --             ------   ----
  22/tcp         ALLOW    Anywhere
  80/tcp         ALLOW    Anywhere
  ```
