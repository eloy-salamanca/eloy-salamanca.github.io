---
layout: post
title: "Testing Project Honolulu Technical Preview 1803"
description: "Playing with next delivery of Project Honolulu Technical Preview 1803"
categories: [ Honolulu, Windows Admin Center ]
image: assets/img/2018/16.1.png
featured: false
author: eloy
comments: true
---

Management of Windows and Windows Server is definitely about to change, proof of it is continuous improvements of Project Honolulu, which is called to unify management hub.

With Honolulu, you will be able to monitor and troubleshoot both Windows 10 PC and Windows Server Systems.

As a unique web-based console, you can get rid of MMC and Server Manager consoles and manage Windows System almost everywhere.

This what's new in this 1803 episode, that I was able to test

**Note:** Don't try to install this software as gateway in a Domain Controller, as this is currently unsupported.
![Warning on installing on a Domain Controller]({{site.baseurl}}/assets/img/2018/16.1.png)

{% include advertising/gl-adsense.html %}

## Windows Updates

The foundation of Honolulu is also updatable via Windows Update, as you can see in example, so you can easily patch systems without even log into them
![Windows Updates]({{site.baseurl}}/assets/img/2018/16.2.png)

## Failover and Hyper-Converged Cluster Manager

Now it's possible to manage both of them; including some new features
![Failover and Hyper-Converged Cluster Manager1]({{site.baseurl}}/assets/img/2018/16.3.png)
![Failover and Hyper-Converged Cluster Manager2]({{site.baseurl}}/assets/img/2018/16.4.png)
![Failover and Hyper-Converged Cluster Manager3]({{site.baseurl}}/assets/img/2018/16.5.png)

## Computer Management

One of the highest demanded features for this version; the ability to manage Windows 10 PCs like traditional Computer Management console.
![Computer Management]({{site.baseurl}}/assets/img/2018/16.6.png)

## Access Control

With no doubt, one of the major new functionality: Azure Active Directory (AAD) based access control has been added. Using AAD identities, you can configure conditional access policies that require Multi-Factor authentication, device compliance, etc.

{% include advertising/gl-adsense.html %}

## Logging

Logging schema was updated to include more details. Events are logged on the target node in event channel Microsoft-ServerManagementExperience with event ID 4000. Here's an example of a failed attempt to access a Windows System
![Logging]({{site.baseurl}}/assets/img/2018/16.7.png)

## Improvements to already available tools

### Certificates

![Certificates1]({{site.baseurl}}/assets/img/2018/16.8.png)
![Certificates2]({{site.baseurl}}/assets/img/2018/16.9.png)

### Files

Ability to download or upload folders, including automatic use of zip/unzip, and create new folders
![Files]({{site.baseurl}}/assets/img/2018/16.10.png)

### Processes

Grouped by default for local Windows 10 PC, and added more information in the details pane: Services, Handles and Modules
![Processes]({{site.baseurl}}/assets/img/2018/16.11.png)

### Services

More settings like startup parameters, user account, and recovery options
![Services1]({{site.baseurl}}/assets/img/16.12.png)
![Services2]({{site.baseurl}}/assets/img/16.13.png)

### Storage Volumes

Now it's possible to manage quotas with FSRM role service (File Server Resource Manager)
![Storage Volumes]({{site.baseurl}}/assets/img/2018/16.14.png)

{% include advertising/gl-adsense.html %}