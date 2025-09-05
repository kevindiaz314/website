---
title: "Installing Proxmox VE 9"
linkTitle: "Proxmox VE 9 Installation"
weight: 2
---

Proxmox Virtual Environment (Proxmox VE) is an open-source virtualization platform that combines container and full virtualization. This guide walks you through the installation process of Proxmox VE 9.

## Installation Process

The Proxmox VE installation is straightforward and consists of several configuration screens. Here's what you'll encounter:

### 1. End User License Agreement

The first screen that appears is the **End User License Agreement (EULA)**. Accept them to proceed with the installation.

### 2. Target Disk and Filesystem Selection

Next, you'll choose:
- **Target disk**: Select the drive where Proxmox VE will be installed
- **Filesystem**: Choose from the available options:
  - **ext4**: Traditional Linux filesystem, reliable and well-tested
  - **xfs**: High-performance filesystem, good for large files
  - **btrfs**: Modern filesystem with advanced features like snapshots
  - **ZFS**: Advanced filesystem with built-in RAID, compression, and data integrity features

{{< callout type="info" >}}
For this installation, **ZFS RAID0** was selected. ZFS provides excellent data integrity features and built-in RAID capabilities, making it ideal for virtualization environments.
{{< /callout >}}

### 3. Location and Keyboard Settings

Configure your regional settings:
- **Country**: Select your country for proper timezone and locale settings
- **Time zone**: Choose your local timezone
- **Keyboard layout**: Select the appropriate keyboard layout for your region

### 4. Administrator Password and Email

Set up the root account:
- **Password**: Enter a strong password for the root user
- **Confirm password**: Re-enter the password to confirm
- **Email**: Provide an email address for system notifications and alerts

{{< callout type="warning" >}}
Choose a strong password as the root account has full administrative privileges over your Proxmox VE system.
{{< /callout >}}

### 5. Management Network Configuration

This is one of the most important configuration steps. You'll configure:

#### Network Interface
- **Management interface**: Select the network port that will be used for Proxmox VE management
- If multiple network ports are available, choose the one connected to your network (indicated by a green dot)
- In single-port setups, only one option will be available and pre-selected

#### Network Settings
- **Hostname (FQDN)**: Enter the fully qualified domain name for your Proxmox server
  - Example: `proxmox.example.local`
  - This hostname will be used to access the web interface and for cluster communication

- **IP Address (CIDR)**: Specify the static IP address for your Proxmox server
  - Example: `10.0.0.10/24`
  - The `/24` indicates a subnet mask of 255.255.255.0
  - Choose an IP address that's within your network range but outside your DHCP pool

- **Gateway**: Enter your network gateway IP address
  - Example: `10.0.0.1`
  - This is typically your router's IP address

- **DNS Server**: Specify the DNS server IP address
  - Example: `10.0.0.1` (using the gateway as DNS)
  - You can also use public DNS servers like `8.8.8.8` or `1.1.1.1`

{{< callout >}}
**Understanding FQDN**: The Fully Qualified Domain Name should resolve to your Proxmox server's IP address. This is important for proper SSL certificate generation and cluster operations.
{{< /callout >}}

{{< callout type="info" >}}
**IP Address Planning**: Choose a static IP address outside your DHCP range to avoid conflicts. Document this IP address as you'll use it to access the Proxmox web interface.
{{< /callout >}}

### 6. Installation Summary

The final configuration screen displays a summary table with all your selected options:
- **Filesystem**
- **Disk(s)**
- **Country**
- **Timezone**
- **Keymap**
- **Email**
- **Management Interface**
- **Hostname**
- **IP CIDR**
- **Gateway**
- **DNS**

Review all settings carefully before proceeding.

#### Auto-reboot Option
At the bottom of the summary screen, you'll find a checkbox:
- **"Automatically reboot after successful installation"** (checked by default)
- Leave this checked for automatic reboot, or uncheck if you want to manually restart

### 7. Installation Progress

During the installation process, the **Virtualization Platform** screen is displayed. This screen provides information about Proxmox VE, including:

- **Open Source Virtualization Platform**: Proxmox VE is completely open source
- **Container Virtualization**: Support for LXC containers
- **Full Virtualization (KVM)**: Support for virtual machines using KVM
- **Additional Resources**: 
  - Visit [proxmox.com](https://www.proxmox.com) for official information
  - Check the [Proxmox VE Wiki](https://pve.proxmox.com/wiki/) for documentation and guides

After the installation completes and the system reboots, you can proceed to access your Proxmox VE installation as described below.

## Post-Installation

Go to the Proxmox VE web interface by navigating to the IP address you set at step 5.

For example, with the IP address used in this guide:
```
https://10.0.0.10:8006
```

Log in using:
- **Username**: `root`
- **Password**: The password you set during installation

## Post-Installation Setup

Now that you've installed Proxmox VE, you can proceed to the  [post-installation setup](../proxmox-post-installation/).

{{< callout type="important" >}}
**Don't skip the post-installation setup!** The guide covers essential configuration steps including repository setup, subscription management, and system optimization that are crucial for a properly functioning Proxmox VE installation.
{{< /callout >}}
