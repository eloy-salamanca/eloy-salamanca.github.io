---
layout: post
title: "Managing Microsof Teams workloads using PowerShell"
description: "How que get Microsoft Teams clean and sorted as the number of teams grows, especially public ones."
categories: [ PowerShell, Microsoft Teams ]
image: assets/img/2019/29.2.png
featured: false
author: eloy
comments: true 
---

There's no doubt Microsoft Teams is being adopted worldwide as a standard in enterprise collaboration streams, and also that is particularly not easy to keep it clean and sorted as the number of teams grows, especially public ones.

PowerShell surely will do a good job here, so let's try it

---

## Install Microsoft Teams PowerShell Module
Opened PowerShell console as administrator, and installed required module

```powershell
Install-Module -Name MicrosoftTeams
```

![Install Microsoft Teams PowerShell Module]({{site.baseurl}}/assets/img/2019/29.1.png)

Checking for availability and commands list

```powershell
Get-Module -Name MicrosoftTeams
Get-TeamHelp
```

![Check for availability]({{site.baseurl}}/assets/img/2019/29.2.png)

For now a bit limited, but surely will be improved next versions

{% include advertising/gl-adsense.html %}

## Connecting to Microsoft Teams tenant
```powershell
Connect-MicrosoftTeams -Account id user@company.com
```

![Connecting to Microsoft Teams tenant]({{site.baseurl}}/assets/img/2019/29.3.png)

---

## Creating Team and add users
Very very easy...firstly, creation of the test team

```powershell
$group = New-Team -DisplayName "string_name" -Visibility "public or private"
```

![Creating new Microsoft Team]({{site.baseurl}}/assets/img/2019/29.4.png)

![Checking created Team]({{site.baseurl}}/assets/img/2019/29.5.png)

Adding users (you've got it; can be scripted!!)
```powershell
Add-TeamUser -GroupId $group.GroupID -User user@company.com
```

![Adding users to the Team]({{site.baseurl}}/assets/img/2019/29.6.png)

![Checking results]({{site.baseurl}}/assets/img/2019/29.7.png)

{% include advertising/gl-adsense.html %}

## Adding Channels
Also one liner...so to be scripted!!

```powershell
New-TeamChannel -GroupID $group.GroupID -DisplayName "String_name" -Description "String_Description"
```

![Adding Channels]({{site.baseurl}}/assets/img/2019/29.8.png)

![Checking results]({{site.baseurl}}/assets/img/2019/29.9.png)

---

## Conclusion
Having in mind this is the first release of the module, I think major commandlets are here, so Microsoft Teams admins will appreciate it to keep their environment clean and sorted. Also, I'm sure this won't be the last version, so thinking in near future, to improve processes and tasks automation (for example on new comers to company) this kind of tooling is something to use on a daily basis.
