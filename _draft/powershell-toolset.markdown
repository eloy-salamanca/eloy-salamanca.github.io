---
layout: post
comments: true
title: PowerShell Toolset
excerpt: Set of PowerShell tools for SysAdmins
date: 2020-01-07 15:12:00 +0300
img:  40.1.png # Add image post (optional)
fig-caption: Set of PowerShell tools for SysAdmins # Add figcaption (optional)
category: powershell
tags: [PowerShell][SysAdmin] # add tag
---

- **Get Windows Defender Status**
```powershell
Get-AntiMalwareStatus -Scope AllServer | Format-Table -Autosize
```

```powershell
Get-MpComputerStatus
```
![Windows Defender Status]({{site.baseurl}}/assets/img/40.1.png)
