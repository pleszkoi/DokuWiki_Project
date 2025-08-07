# Virtualization Setup

Virtualization makes possible to run multiple isolated environments (Virtual Machines) on a single physical machine. That is ideal for creating a homelab environment because it is cost-effective, flexible and easy to manage.


## Overview

In this project, my goal was to install and configure a documentation system (DokuWiki) on a Linux-based virtual machine. Using virtualization allows for:
- Resource isolation: The VM runs independently from the host system.
- Snapshot creation: The previous state can be restored at any time.
- Easy migration and experimentation: Try out new configurations without any risks.

The chosen virtualization solution:
VirtualBox: It is free, platform-independent and supports most features (snapshots, bridged networking).

Purpose of use:
Running a Linux-based server on which the documentation system and the related services operate.


## Host Machine Specifications

The homelab environment is based on a physical machine running the virtualization platform.
Below are the main specifications of the host:

- Hardware specifications
    - CPU: 12th Gen Intel(R) Core(TM) i5-1235U
    - RAM: 15Gi
    - Storage: 137Gb
    - Network: Gigabit Ethernet + Wifi

- OS version:  Ubuntu 24.04.2 LTS

This configuration allows running multiple virtual machines, but the allocation of the RAM and CPU cores requires optimization. In the test environment, it is planned to run 1-2 VMs in parallel.


## Virtualization Software Details

This section contains the virtualization platform and the applied configuration settings.

- Virtualization software: VirtualBox Graphical User Interface 
- Version: 7.0.16

The setting of the network was set to "Bridged Adapter" mode so that the virtual machine is on the same network as the host and can be accessed from other devices.


## Virtual Machine Specifications

| VM Name          | OS                 | CPU    | RAM    | Disk Size  | IP Address   | Hostname       |
|------------------|--------------------|--------|--------|------------|--------------|----------------|
| Ubuntu DocServer | Ubuntu 22.04.5 LTS | 2 vCPU | 2 Gi   | 15Gb       | 192.168.1.88 | dokuwiki.local |

Allocating the VM's resources I have adjusted the performance of the host machine. 2 vCpus an 2 Gi RAM are optimal for installing and running DokuWiki.


## Network Configuration

To access virtual machines over the network, it is important to set the correct network mode and the IP address.

- Network mode:
    - Used network mode: Bridged Adapter
    - Connected interface: Physical nework card (Ethernet / Wi-Fi)

- Setting static IP address

Modifying the configuration file found in the /etc/netplan/ directory.

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.1.88/24
      gateway4: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
```

Applying the configuration:

```bash
sudo netplan apply
```

Examining:

- Checking the IP address:

```bash
ip a
```

- Checking the connection:

```bash
ping 192.168.1.1
ping google.com
```


## Storage and Snapshots

- Disk Allocation
    - Disk Type: VDI (VirtualBox Disk Image)
    - Storage Mode: Dynamically Allocated
    - Disk Size: 15 Gb
    - Storage Location: /home/istvan/VirtualBox VMs/Ubuntu DocServer/

- Snapshots
    - Purpose: Create a pre-installation backup point.
    - Snapshot Name: Initial Setup
    - Creation Date: 2025.08.06.

- Backup Strategy

Exporting the Virtual Machine and the snapshots in .ova format weekly, copying them to an external drive. Only the documentation is saved in Git.
