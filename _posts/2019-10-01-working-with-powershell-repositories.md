---
layout: post
title: "Working with PowerShell Repositories"
description: "Important to keep our work with PowerShell repositories, keeping them updated to the latest and to avoid bugs"
categories: [ powershell, IaaC, NuGet, Orquestration ]
image: assets/img/2019/34.1.png
featured: false
author: eloy
comments: true
---

Package Management is becoming more and more important these days to keep the ball rolling; this is a first approach on working with PowerShell Repositories, using **NuGet Gallery** and latest [PowerShell v7.0.0-preview4](https://github.com/PowerShell/PowerShell/releases){:target="_blank"}


PowerShell7 comes with `PackageManagement` Module, which is basic for my purpose:
```powershell
Get-Command -Module PackageManagement
```

Also, **NuGet** and **PowerShell Gallery** are included:
```powershell
Get-PackageSource
```

To know further details about a particular repository:
```powershell
Get-PSRepository -Name "PSGallery" | Format-List * -Force
```

{% include advertising/gl-adsense.html %}

Last but not least, important to keep modules updated (not applicable in this example)
```powershell
Update-Module
```

![Working with PowerShell Repositories]({{site.baseurl}}/assets/img/2019/34.1.png)
