---
layout: post
title: "Install Windows 10 RSAT feature by using PowerShell"
description: "How to install Windows 10 RSAT feature by only using PowerShell"
categories: [ powershell,  ]
image: assets/img/2018/26.2.png
featured: false
author: eloy
comments: true 
---

RSAT (Remote Server Administration Tools), continue being key set of tools in today's Windows System Administration, especially in large environments.

Windows 10 features are now being updated more than never, so it's important to make sure you have the latest set of tools along with you, and it's a plus if can get them updated in the fast and easy way.

Starting with Windows 10 1809, it's possible to update your RSAT using PowerShell, so dropping here last upgrading process I made to my **Windows 10 1809** platform:

{% include advertising/gl-adsense.html %}

## Check for current version of RSAT
To start with, I checked my current version of Windows 10, just in case:

```powershell
Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion' | Select ProductName, ReleaseID, CurrentBuild, UBR
```

![Check current version of RSAT]({{site.baseurl}}/assets/img/2018/26.1.png)

I realized I won't be able to take advantage of new way of upgrading RSAT, because **ReleaseId is 1803**, so *upgrade this system to 1809 was necessary*.

## See available set of RSAT tools
After upgraded previous system to 1809, I needed to start PowerShell session as Administrator; and as I wanted to see list of RSAT available till the moment, I called the full list:

```powershell
Get-WindowsCapability -Online | ? Name -like RSAT* | FT
```

![See available set of RSAT tools]({{site.baseurl}}/assets/img/2018/26.2.png)

{% include advertising/gl-adsense.html %}

## Installing RSAT tools

```powershell
Get-WindowsCapability -Name RSAT* -Online | Add-WindowsCapability -Online
```

![Installing RSAT tools]({{site.baseurl}}/assets/img/2018/26.3.png)

## Check for specific tools

If you want to check for specific tools of see which of them are not present in your system and even install them, you can use this:
```powershell
Get-WindowsCapability -Online | ? ($_.Name -like "RSAT*" -and $_.State -eq "NotPresent"} | Add-WindowsCapability -Online
```

![Check for specific tools]({{site.baseurl}}/assets/img/2018/26.4.png)
