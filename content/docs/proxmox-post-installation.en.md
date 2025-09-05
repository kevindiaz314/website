---
title: "Proxmox VE 9 Post-Installation Setup"
linkTitle: "Post-Installation Setup"
weight: 3
---

After completing the initial installation of Proxmox VE 9, there are several important configuration steps to optimize your system for home lab use. This guide walks you through the essential post-installation tasks using the community-maintained helper script.

## Optional: Installing Fish

Personally, I like to use the Friendly Interactive Shell for a better command-line experience, but this is entirely optional and not required for the post-installation process. If you want to try it, you can install it with:

```bash
apt install fish -y
```

{{< callout type="info" >}}
Fish provides syntax highlighting, autocompletion, and overall user experience compared to the default Bash.
{{< /callout >}}

## Proxmox VE Post-Install Helper Script

We'll use a community-maintained [Proxmox VE Helper-Scripts](https://community-scripts.github.io/ProxmoxVE/scripts?id=post-pve-install) to automate most configuration tasks.

### Running the Script

Execute the following command to download and run the script:

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/post-pve-install.sh)"
```

### Script Execution Process

When you run the script, you'll see the ASCII art banner and be prompted to start:

```
    ____ _    ________   ____             __     ____           __        ____
   / __ \ |  / / ____/  / __ \____  _____/ /_   /  _/___  _____/ /_____ _/ / /
  / /_/ / | / / __/    / /_/ / __ \/ ___/ __/   / // __ \/ ___/ __/ __ `/ / /
 / ____/| |/ / /___   / ____/ /_/ (__  ) /_   _/ // / / (__  ) /_/ /_/ / / /
/_/     |___/_____/  /_/    \____/____/\__/  /___/_/ /_/____/\__/\__,_/_/_/

This script will Perform Post Install Routines.

Start the Proxmox VE Post Install Script (y/n)?
```

**Answer: `y`**

## Configuration Steps

The script will guide you through several configuration options. Here's what each step configures:

### 1. Repository Configuration

#### Enterprise Repository Removal
The script will detect existing enterprise repositories and ask what to do:

```
'pve-enterprise' repository already exists.
What do you want to do? 
keep    Keep as is
disable Comment out (disable) this repo
delete  Delete this repo file.
```

**Recommended choice: `delete`**

{{< callout type="warning" >}}
Enterprise repositories require a valid Proxmox subscription. For home labs and non-commercial use, it's recommended to delete them to avoid update errors.
{{< /callout >}}

You'll get the same prompt for the 'ceph enterprise' repository:
**Recommended choice: `delete`**

#### No-Subscription Repository
```
The 'pve-no-subscription' repository provides access
to all of the open-source components of Proxmox VE. 

Add 'pve-no-subscription' repository (deb822)?
```

**Recommended choice: `yes`**

{{< callout >}}
The no-subscription repository provides stable updates for Proxmox VE without requiring a commercial subscription.
{{< /callout >}}

#### Test Repository
```
The 'pve-test' repository can give advanced users
access to new features and updates before they are
officially released.

Add (Disabled) 'pvetest' repository (deb822)?
```

**Recommended choice: `no`**

{{< callout type="info" >}}
The test repository contains bleeding-edge features that may not be stable. Only enable this in development environments.
{{< /callout >}}

### 2. Subscription Nag Removal

```
This will disable the nag message reminding you to
purchase a subscription every time you log in to the
web interface.
Disable subscription nag?
```

**Recommended choice: `yes`**

{{< callout >}}
This removes the subscription popup that appears when accessing the web interface without a commercial subscription.
{{< /callout >}}

The script will show a support message:
```
Supporting the software's development team is essential. Check their official website's Support Subscriptions for pricing. Without their dedicated work, we wouldn't have this exceptional software.
```

**Press Enter to acknowledge**

### 3. High Availability Configuration

```
HIGH AVAILABILITY

If you plan to utilize a single node instead of a clustered environment, you can disable unnecessary high availability (HA) services, thus reclaiming system resources.

If HA becomes necessary at a later stage, the services can be re-enabled.

Disable high availability?
```

**For single-node setups: `yes`**

**For cluster environments: `no`**

{{< callout type="info" >}}
High Availability services are only needed in multi-node cluster environments. Disabling them on single-node installations frees up system resources.
{{< /callout >}}

### 4. Corosync Configuration

```
COROSYNC
Disable Corosync for a Proxmox VE Cluster?
```

**For single-node setups: `yes`**

**For cluster environments: `no`**

{{< callout >}}
Corosync is the cluster communication layer. It's not needed for single-node installations but is essential for clusters.
{{< /callout >}}

### 5. System Update

```
UPDATE
Update Proxmox VE now?
```

**Recommended choice: `yes`**

The script will update your system packages to the latest versions.

## Script Completion Summary

After all configurations are applied, you'll see a summary:

```
 ✓ Deleted 'pve-enterprise' repository file
 ✓ Deleted 'ceph enterprise' repository file
 ✓ Added 'pve-no-subscription' repository
 ✓ 'ceph' package repository (no-subscription) already exists (skipped)
 ✗ Selected no to Adding 'pvetest' repository
 ✓ Disabled subscription nag (Delete browser cache)
 ✓ Disabled high availability
 ✓ Disabled Corosync
 - Updating Proxmox VE (Patience)...
```

### Post-Install Reminder

The script will display an important reminder:

```
┌──────────────────────────┤ Post-Install Reminder ├───────────────────────────┐
│ IMPORTANT:                                                                   │ 
│                                                                              │ 
│ If you have multiple Proxmox VE hosts in a cluster, please make sure to run  │ 
│ this script on every node individually.                                      │ 
│                                                                              │ 
│ After completing these steps, it is strongly recommended to REBOOT your      │ 
│ After the upgrade or post-install routines, always clear your browser cache  │ 
│ or perform a hard reload (Ctrl+Shift+R) before using the Proxmox VE Web UI   │ 
│                                    <Ok>                                      │ 
└──────────────────────────────────────────────────────────────────────────────┘
```

**Press Enter to acknowledge**

### System Reboot

```
REBOOT
Reboot Proxmox VE now? (recommended)
```

**Recommended choice: `yes`**

{{< callout type="important" >}}
A reboot is highly recommended after the post-installation configuration to ensure all changes take effect properly.
{{< /callout >}}

## Final Summary

The completed post-installation will show:

```
 ✓ Deleted 'pve-enterprise' repository file
 ✓ Deleted 'ceph enterprise' repository file
 ✓ Added 'pve-no-subscription' repository
 ✓ 'ceph' package repository (no-subscription) already exists (skipped)
 ✗ Selected no to Adding 'pvetest' repository
 ✓ Disabled subscription nag (Delete browser cache)
 ✓ Disabled high availability
 ✓ Disabled Corosync
 ✓ Updated Proxmox VE
 ✓ Completed Post Install Routines
```

## Post-Reboot Steps

After the system reboots:

1. **Clear browser cache** or perform a hard reload (Ctrl+Shift+R) before accessing the web interface
2. **Verify web access** at `https://your-proxmox-ip:8006`
