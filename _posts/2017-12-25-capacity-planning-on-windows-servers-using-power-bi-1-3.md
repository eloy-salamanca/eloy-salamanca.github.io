---
layout: post
title: "Capacity Planning on Windows Servers using Power BI 1"
description: "How to enable do make Capacity Planning on Windows Servers using PowerShell and Power BI"
categories: [ powershell, Capacity Plan ]
image: assets/img/2020/36.1.png
featured: false
author: eloy
comments: true
---

When it comes to controlling the growth of Windows Systems it’s not an easy one, especially if you want to keep it simple, fast and cheap.

As I’m been requested to summarize this info several times a year in specific reports, I decided to try and automate this process as much as possible.

I designed 3 PowerShell scripts:

* **Set-CapRepDB.ps1**: To create a new db on selected SQL Server almost automatically.
* **Set-ServerCapRep.ps1**: To deploy a PowerShell agent massively on every single Windows System and schedule intervals to run.
* **Add-DataServerPeakAgent.ps1**: Agent on every Windows System to push data into db at scheduled times

Lastly, after some time agents running on, you will be able to check all collected info with nice visuals and analytics thanks to Power BI, and even download into excel file for reporting, for example.

Let’s start this episode with first PowerShell script; next deliveries will include bricks of this building.

{% include advertising/gl-adsense.html %}

## 1.Build the database

[Set-CapRepDB.ps1](https://github.com/eloysr/CapRep/blob/master/Set-CapRepDB.ps1){:target="_blank"} script and [CapRep_MASTER.sql](https://github.com/eloysr/CapRep/blob/master/CapRep_MASTER.sql){:target="_blank"} will let you create the basis of this Capacity Planning.

You can, of course, modify at your own to fit your needs; this is only one tested way of working.

PS: You should edit both files to point to appropriated user/password/db… etc

An example, after running it:

```powershell
.\Set-CapRepDB.ps1 -verbose
```

![Build the database]({{site.baseurl}}/assets/img/2020/36.1.png)

{% include advertising/gl-adsense.html %}