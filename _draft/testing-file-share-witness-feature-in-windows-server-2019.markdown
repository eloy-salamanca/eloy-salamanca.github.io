---
layout: post
comments: true
title: Testing File Share Witness Feature in Windows Server 2019
date: 2018-05-29 09:12:00 +0300
description: Trying File Share Witness Feature on Windows Server 2019
img: # Add image post (optional)
fig-caption: # Add figcaption (optional)
categories: [failover-clustering]
tags: [SysAdmin] # add tag
---
[https://blogs.msdn.microsoft.com/clustering/2018/04/16/new-file-share-witness-feature-in-windows-server-2019/](https://blogs.msdn.microsoft.com/clustering/2018/04/16/new-file-share-witness-feature-in-windows-server-2019/)


One of the most interesting features I've been waiting for is the ability to setup Windows Clusters detached from Active Directory, especially useful for virtualized environments, where your Active Directory domain controllers are VMs hosted into different physical hosts, and still would like to get those bare-metal hosts joined to a failover cluster. It's true that starting early installment of Windows 2016 it was available for Datacenter version, but now also for standard version.

For this and other specific scenarios (poor internet connection, resource nodes behind DMZ, no domain controller connection available, etc) **Windows Server 2019** has set up new feature **File Share Witness**.

I’ve been playing a bit with **File Share Witness** for testing this new feature, and I must say it promises well; this is what I did

PS: This type of File Share Witness can only be created through PowerShell for now.


## Create local user account
Let’s take for example, I have a server called SERVER and a share called SHARE I want to utilize as the File Share Witness. The steps for setting this up are:
1. Log on to SERVER and create a local user account (i.e. FSW-ACCT)
2. Create a folder on SERVER and share it out
3. Give the local user account (FSW-ACCT) full rights to the share
4. Log in to one of your cluster nodes and run the PowerShell command:

```powershell
Set-ClusterQuorum -FileShareWitness \\SERVER\SHARE -Credential $(Get-Credential)
```
5. You will be prompted for the account and password for which you should enter SERVER\FSW-ACCT and the password.

Voilá!! You are done as we just took care of all the above scenarios. The cluster will keep the name and password encrypted and not accessible by anyone.

Just as an FYI, the original demo of this feature was using a USB thumb drive holding the share plugged into an SMB router. Not that we would recommend doing it, but does show how far you could take this.”

