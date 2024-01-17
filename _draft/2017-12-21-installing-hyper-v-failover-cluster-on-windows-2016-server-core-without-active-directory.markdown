---
layout: post
comments: true
title: Installing Hyper-V Failover Cluster on Windows 2016 Server Core without Active Directory
excerpt: How to install Hyper-V Failover Cluster on Windows 2016 Server Core without Active Directory
date: 2017-12-21 10:46:00 +0300
img: 38.4.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
category: hyper-v
tags: [PowerShell, Windows Server, Windows Server Core, Active Directory] # add tag
---

After Windows 2016 Server Core installed on a bare metal server, let’s go throughout the installation process of all required components:

* **Network settings**: After entering new password, time to configure network settings, Workgroup, ComputerName and Remote Management. Type "sconfig" on cmd console to access following screen and configure all different aspects accordingly; something like this (PS: you're going to need fixed IP Address):

![Networking settings]({{site.baseurl}}/assets/img/38.1.png)

Advisable to update to the latest patch level available till the date.

![Patching level2]({{site.baseurl}}/assets/img/38.2.png)

* Each cluster node needs to have a primary DNS suffix, so going to achieve this with regedit's help:

```
HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters
Domain (reg_sz): workgroup.local
SearchList (reg_sz): workgroup.local
```

![Modifying Regedit]({{site.baseurl}}/assets/img/38.3.png)

After reset, you should see following values on running ipconfig /all

![Checking results]({{site.baseurl}}/assets/img/38.4.png)

* Install required roles: Failover Cluster, Hyper-V, DNS from PowerShell

```bash
powershell.exe
install-windowsfeature -name failover-clustering, hyper-v, dns -includemanagementtools
```

![Installing Failover Clustering Feature]({{site.baseurl}}/assets/img/38.5.png)

* Creating DNS Zone and A records: This is required because of created an Active-Directory detached Hyper-V Cluster

```bash
powershell.exe
add-dnsserverprimaryzone -zonename workgroup.local -zonefile workgroup.local.dns
add-dnsserverresourcerecorda -name cluster -zonename workgroup.local -ipv4address 10.40.100.90
get-dnsserverresourcerecord -zonename "workgroup.local"
```

![DNS Zone and A records]({{site.baseurl}}/assets/img/38.6.png)

* **Setting up storage (iSCSI target)**: First step is to create 2 volumes (quorum and data1), iSCSI storage, and present both to the future Hyper-V Cluster.

To achieve this, let's define both volumes in a different Windows 2016 (Desktop experience), and present it to the Hyper-V future Cluster.

```
quorum - C:\quorum\quorum - 500MB
hvdata1 - C:\hvdata1\hvdata1 - 50GB
```

Now that we have 2 volumes available by iSCSI, let’s present them to our Windows 2016 Core:
```powershell
Set-Service -Name msiscsi -StartupType Automatic
Start-Service msiscsi
New-iscsiTargetPortal -TargetPortalAddress 192.168.250.3
```

![New SCSI]({{site.baseurl}}/assets/img/38.7.png)

Check for iSCSI available targets:

8.1

Next, connecting to specific iSCSI targets we created previously:

```powershell
Connect-IscsiTarget -nodeaddress iqn.1991-05.com.microsoft:storage1-quorum-target -IsPersistent $true -IsMultipathEnabled $true -InitiatorPortalAddress 10.40.100.99 -TargetPortalAddress 10.40.100.4
Connect-IscsiTarget -nodeaddress iqn.1991-05.com.microsoft:hvdata1 -IsPersistent $true -IsMultipathEnabled $true -InitiatorPortalAddress 10.40.100.99 -TargetPortalAddress 10.40.100.4
Connect-IscsiTarget -nodeaddress iqn.1991-05.com.microsoft:hvdata1 -IsPersistent $true -IsMultipathEnabled $true -InitiatorPortalAddress 10.40.100.99 -TargetPortalAddress 10.40.100.4
```

8.2

Testing that everything was resulted OK:

8.3

From now on, we will have our first Hyper-V host connected to storage, so 3 disks available to create Hyper-V Cluster.

## Creating Hyper-V Cluster
