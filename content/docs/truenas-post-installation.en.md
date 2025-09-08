---
title: "TrueNAS Community 25.10 (Goldeye) Post-Installation"
linkTitle: "TrueNAS Post-Installation"
weight: 4
---

{{< callout type="info" >}}
Installing TrueNAS Community is fairly straightforward and self‑guided through the installer.
So this is not a strict tutorial. It’s a documentation of how I configure my home server. There are many approaches to set up TrueNAS, this is simply the way I do it.
{{< /callout >}}

## Change the Timezone

TrueNAS defaults to Los Angeles. Update it to your local time zone.

- Go to System → General Settings → Localization

## Create a Storage Pool

1. Go to Storage → Pools → Add → Create new pool
2. I choose the most typical name for a pool: `tank`
3. Layout: I select Mirror (I have two SSDs)
4. Disk size: with two identical 2 TB SSDs, the selectable option is typically “1.82 TiB (SSD)”
5. Width defaults to 2 (matching two disks) and Number of VDEVs defaults to 1 — I leave both as is
6. I click Next → optional steps left at defaults → Save And Go To Review
7. Review settings → Create Pool
8. Confirm the warning: “The contents of all added disks will be erased.”

{{< callout type="tip" >}}
It’s good practice to use identical disks for a pool. Even when two disks are labeled the same capacity, their actual usable sizes can differ slightly. The pool capacity is limited by the smallest disk, so matching drives avoids surprises.
{{< /callout >}}

## Create a User Account

Navigate to Credentials → Users, then click Add (top‑right).

Documentation reference: [Managing Users](https://www.truenas.com/docs/scale/scaletutorials/credentials/managelocalusersscale/)

{{< callout type="info" >}}
That page may default to the "Next" (development) version. Use the version selector in the top‑right to switch to your installed release.
{{< /callout >}}

- Required fields are marked with an asterisk
- Email isn’t required, but I like to include it
- UID is prefilled with 3000 — I leave it unchanged

> Help: UID — User accounts have an ID greater than 1000 and system accounts have an ID equal to the default port number used by the service.

- Auxiliary Groups is optional, but I add `builtin_administrators` to give the my user admin privileges.
- Create new primary group: Yes (default)

### Home Directory and Permissions

For Home Directory, I point it at a dataset under `tank`:

1. Click the dataset picker → choose `tank`
2. Click Create Dataset and name it with the username you chose before
3. Leave “Create Home Directory” unchecked

{{< callout >}}
Why this approach? I simply prefer seeing the home dataset explicitly under Datasets, which makes things more organized in my opinion.
{{< /callout >}}

### Authentication and Shell

TrueNAS has a built-in terminal, but I prefer to use SSH to connect to my server.

- Authorized Keys: paste my SSH public key
- Shell (required): select `zsh` if you want. I wish I could use `fish` but it's not supported yet.
- Check “Allow all sudo commands” and “Allow all sudo commands with no password”
If you feel **_adventurous_**.

{{< callout type="warning" >}}
Granting passwordless sudo is not really a good choice. I do this because it's convenient when working in the terminal, but the GUI is the only recommended management method in TrueNAS.
{{< /callout >}}

- SMB User: leave checked if you want to use SMB to access your files from other devices. Which probably you most likely will. That's why you installed a NAS in the first place.

You’ll see a confirmation:

> “With this configuration, the existing directory `/mnt/tank/<username>` will be used as a home directory without creating a new directory for the user.”

Confirm and continue.

Finally, log out of `truenas_admin` and log in with your new user.

{{< callout type="warning" >}}
If you added your SSH public key, you need to enable SSH in the Services section.
{{< /callout >}}

## Personal UI Preferences

- Theme: set to Dracula (User menu → Preferences → Theme)
- Session timeout: increase from 5 minutes to 1 hour (3600 seconds) (User menu → Preferences)
- Enable "Redirect to HTTPS" and "Show Console Messages" I do this as a quality of life improvement.

## Prepare Datasets for Apps

I create an `apps` dataset and preset it for applications:

1. Datasets → `tank` → Add Dataset
2. Name: `apps`
3. Dataset Preset (required): In this case I select “Apps” → Save

Tip I follow: I name datasets in lowercase to make CLI usage and `compose.yaml` file paths easier to type.

Create sub‑datasets under `apps` for each application you install later (e.g., `apps/portainer`).

## Configure Scheduled Scrubs

I configure my pool’s scrub schedule and adjust the threshold:

- Go to Storage → Storage Health → Schedule Scrubs → Configure
- Change Threshold Days from 35 to 30

> It's more convinient if you search for the option in the search bar at the top of the page.

## Enable the Apps Service

Go to Apps. If you see “Apps Services Not Configured” (top‑right near the gear):

1. Click Configuration → Choose Pool
2. Select `tank` or the name of your pool
3. Status should change to “Apps Services Running”

## Network Settings

Since I have two TrueNAS servers, I set a hostname to distinguish them.

- Network → Global Configuration → Hostname (e.g., `truenas01`, `truenas02`)

## Email Notifications (SMTP)

Placeholder: I’ll document SMTP setup separately and link it here when ready.

{{< callout type="info" >}}
Email (SMTP) setup guide: coming soon.
{{< /callout >}}

## First App: Portainer

I install Portainer first as my container management interface. The setup includes IP binding for a cleaner homelab experience.

Check out the [Portainer installation guide](../portainer-installation/) for detailed step-by-step instructions.