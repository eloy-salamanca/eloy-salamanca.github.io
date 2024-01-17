---
layout: post
title: "Security tip Disable PowerShell 2 engine in latest Windows 10 Builds"
description: "how to remove PowerShell 2.0 and avoid security risks."
categories: [ powershell ]
image: assets/img/2019/33.1.png
featured: false
author: eloy
comments: true
---

The less old stuff you have in your system, the more secure you will be; it doesn’t make a lot of sense having different PowerShell versions in your computer at the same time, so you’d better remove PowerShell 2.0 as soon as possible and avoid security risks.

This is and easy to do it, by the way using latest PowerShell 7.0.0 preview-1, as you can see at the screenshot

{% include advertising/gl-adsense.html %}

## Check current status
```powershell
Get-WindowsOptionalFeature -Online -FeatureName MicrosoftWindowsPowerShellV2
```

## Disabling PowerShell 2.0
```powershell
Disable-WindowsOptionalFeature -Online -FeatureName MicrosoftWindowsPowerShellV2Root
```

![Disabling PowerShell 2.0]({{site.baseurl}}/assets/img/2019/33.1.png)
