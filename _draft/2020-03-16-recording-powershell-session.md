---
layout: post
comments: true
title: Enabling Remote PowerShell from Script on earlier and new PowerShell versions
excerpt: How to enable PowerShell remotely to run scripts
date: 2018-01-10 10:35:00 +0300
img: 11.1.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
category: powershell
tags: [PowerShell] # add tag
---

Lately, I had to execute Powershell very simple script on a large list of Windows systems, but lot of them weren't ready for it, so I ended up having to do some hacking, and previously checking if the system was ready to accept external commands.


Clone GitHub 'CaptureIT' repository:
```powershell
git clone https://github.com/MSAdministrator/CaptureIT.git
```



![Enable Remote PowerShell]({{site.baseurl}}/assets/img/11.1.png)
