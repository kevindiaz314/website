---
title: "Installing Portainer on TrueNAS Community Edition with IP Binding"
linkTitle: "Portainer Installation"
weight: 5
---

{{< callout type="info" >}}
This is more than just a tutorial on how to install Portainer because I'll be using a recently introduced feature in TrueNAS that allows you to bind apps to an IP address. This setup eliminates the need for port numbers in URLs and creates a cleaner, more organized homelab environment.
{{< /callout >}}

## Prerequisites

Before we begin, make sure you've completed the [TrueNAS post-installation setup](../truenas-post-installation/). We'll be building on those configurations, particularly the apps dataset we created.

## Create a Dataset for Portainer

The first thing to do is create a new dataset for the app. In my case, I'll create it inside the `apps` dataset we set up earlier.

1. Go to Datasets ▸ click on the `apps` dataset
2. Click **Add Dataset**
3. Name: `portainer`
4. Leave other settings as default ▸ Save

## Add a Bridge Network Interface

Now for the interesting part - we'll create a bridge network that allows us to bind Portainer to its own IP address.

{{< callout type="info" >}}
More information can be found in the TrueNAS documentation: [Setting up a Network Bridge](https://www.truenas.com/docs/scale/25.10/scaletutorials/network/interfaces/settingupbridge/)
{{< /callout >}}

### Creating the Bridge

1. Go to System ▸ Network ▸ Interfaces ▸ Add
2. **Type**: Select `Bridge`
3. **Name**: `br0` (because we programmers like to start counting from 0)
4. **Description**: `Bridge Network for Apps` (optional, but here we like to follow best practices)

{{< callout >}}
Keep in mind that the network name must start with `br` followed by a unique number.
{{< /callout >}}

### IP Address Configuration

**Define Static IP Addresses** is selected by default, so we'll leave it that way.

Now we'll add the IP addresses:

1. **First IP Address**: This must be the IP address already assigned to your TrueNAS interface
   - For me, it's `10.21.30.100/24`

    Side note: I like to use class A networks because they're easier to remember and save some keystrokes

2. **Second IP Address**: This will be dedicated to Portainer
   - I'll use `10.21.30.101/24`

    Side note: I increment the last octet for simplicity and so I know this IP address is associated with this TrueNAS server (I have two servers).

{{< callout type="warning" >}}
Make sure that the address you choose is not already in use. This is very important because if it is, you'll get a conflict on your router.
{{< /callout >}}

3. **Bridge Members**: Select your existing network interface
4. Leave the rest of the settings as default
5. Click **Save**

### Testing the Network Changes

You'll see two buttons: **Test Changes** and **Revert Changes**.

1. Click **Test Changes**
2. If everything is configured correctly, the page will reload and you'll be able to save the changes
3. If something goes wrong, don't worry - the changes are automatically reverted after 60 seconds

## Configure TrueNAS GUI IP Binding

Now technically we could already bind the app to the `.101` IP address, but there's a caveat: we can't use port 443 because the TrueNAS GUI now can be accessed on both IP addresses (`.100` and `.101`). 

To fix this, we need to bind the TrueNAS GUI to only one IP address:

1. Go to System ▸ General Settings ▸ GUI
2. **Web Interface IP Address**: Change to `10.21.30.100`
3. While we're here, if you didn't read my [TrueNAS post-installation guide](../truenas-post-installation/), you might want to configure:
   - **HTTP Redirect to HTTPS**
   - **Show Console Messages**
   - And adjust your theme options
4. Click **Save**

Now the TrueNAS GUI will only be accessible via the `.100` IP address, freeing up port 443 on the `.101` IP address for Portainer.

## Install Portainer

We're ready to install Portainer! 

1. Go to **Apps** ▸ Search for `Portainer` ▸ Click **Install**
2. The Community Edition image is set by default - this is fine

### Network Configuration

1. **Publish port on the host for external access** is selected by default - leave it that way
2. **Port**: Change from the default port to `443`
3. Click **Add** next to **Host IPs**
4. Select the `10.21.30.101` IP address
5. Leave the rest as default

### Storage Configuration

Under **Storage Configuration**:
1. **Portainer Storage**: Change to `Host Path`
2. Select the dataset you created earlier (`/mnt/tank/apps/portainer`)

### Resources Configuration

The last thing to configure is **Resources Configuration** if you want to adjust it. Personally, I think 2 CPUs and 4 GB of memory is too much for Portainer, so I'll change the memory to 1 GB.

Click **Install**.

## Access Portainer

Once the installation completes:

1. Click the **HTTPS** button in the **Application Info** section
2. This will open a new tab with the Portainer GUI
3. You'll need to accept the security warning as Portainer uses self-signed certificates - this is fine

{{< callout type="warning" >}}
Remember to go through the initial setup within 5 minutes of installing the app. Otherwise, Portainer will block the configuration for security reasons and you'll have to restart the container (app).
{{< /callout >}}

## Why This Setup is Awesome

What I really like about this setup is that we can access Portainer on its own IP address without having to type any port number. Since I use Bitwarden as my password manager, I use the `Ctrl + Shift + L` shortcut to automatically fill the username and password.

I don't have to reach for the mouse, click on the Bitwarden extension, search for the Portainer credentials, and click autofill because I would have two accounts (TrueNAS and Portainer), and even more if I install more apps that bind to the TrueNAS IP address and only use a different port number.

Removing friction from my workflow is huge for me.