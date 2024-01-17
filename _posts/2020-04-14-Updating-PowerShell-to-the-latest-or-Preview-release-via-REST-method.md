---
layout: post
title: "Updating PowerShell to the latest or Preview release via REST method"
description: "How to upgrade PowerShell to the last version throughout internet"
categories: [ powershell ]
image: assets/img/2020/43.1.png
featured: false
author: eloy
comments: true
---

* Latest stable version:
```powershell
Invoke-Expression "& { $(Invoke-RestMethod 'https://aka.ms/install-powershell.ps1') }"
```

* Latest Preview version
```powershell
Invoke-Expression "& { $(Invoke-RestMethod 'https://aka.ms/install-powershell.ps1') } -UseMSI -Preview"
```

![Updating PowerShell to Preview release]({{site.baseurl}}/assets/img/2020/43.2.png)
