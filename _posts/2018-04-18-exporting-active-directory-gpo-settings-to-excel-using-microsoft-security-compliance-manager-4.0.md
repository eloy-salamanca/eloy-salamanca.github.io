---
layout: post
title: "Exporting Active Directory GPO settings to Excel using Microsoft Security Compliance Manager 4.0"
description: "How to successfully export GPO settings to Excel using Microsoft Security Compliance Manager 4.0"
categories: [ Active Directory, GPO, SCCM, excel ]
image: assets/img/2018/14.11.png
featured: false
author: eloy
comments: true
---
Lately, I had to get all GPOs and settings from Active Directory, and it wasn’t as easy as I thought in the beginning, because I wanted to get excel files with all current settings for each GPO.

After trying different options, I ended up using Microsoft Security Compliance Manager 4.0 (SCM), and apart from different minor issues with specific GPOs (honestly, it seems SCM is not very mature right now), I can confirm it’s very easy to use, and results are not bad.

This is what I did:

## 1.Backing up all GPO settings

You can also do it, of course, with Powershell, but I used GPMC this time because it’s pretty straightforward:

I went to GPMC console, right click on ‘Group Policy Objects’, then ‘Back Up All’

![Backing up GPO settings]({{site.baseurl}}/assets/img/2018/14.1.png)  

![choose destination]({{site.baseurl}}/assets/img/2018/14.2.png)  

![Process]({{site.baseurl}}/assets/img/2018/14.3.png)  

{% include advertising/gl-adsense.html %}

## 2.Enabling Macros in Excel

Prior starting exporting GPOs, a bit risky for a while, but I had no option than **enable all macros in Excel**, so I went to **File** > **Options** > **Trust Center** > **Trust Center Settings**, and “**Enable all macros**”
![Excel Trust Center]({{site.baseurl}}/assets/img/2018/14.4.png)  
![Enable all macros]({{site.baseurl}}/assets/img/2018/14.5.png)  

## 3.Microsoft Security Compliance Manager 4.0 (SCM):

**Pre-requisites**:
* Download [Microsoft Security Compliance Manager 4.0 (SCM)](https://www.microsoft.com/en-us/download/details.aspx?id=53353){:target="_blank"}
* Requirements to be installed:
  * SQL Server database: SCM comes with **SQL Server 2008 Express**; if it detects **SQL Server** on the system, you can opt for using it
* Other requirements, besidess this:
  * .NET Framework 3.5
  * Microsoft Visual C++ 2010 x86
  
After installing, first opening took some time because of importing baselines<br>

![Import baselines]({{site.baseurl}}/assets/img/2018/14.6.png)

![Checking for updates]({{site.baseurl}}/assets/img/2018/14.7.png)
![Checking for updates2]({{site.baseurl}}/assets/img/2018/14.8.png)
![Checking for updates3]({{site.baseurl}}/assets/img/2018/14.9.png)
![Checking for updates4]({{site.baseurl}}/assets/img/2018/14.10.png)

{% include advertising/gl-adsense.html %}

## 4.Import previous backed GPOs

Once everything was ready, time to import GPOs previously backed up in step-1</br>

![Import backed GPOs]({{site.baseurl}}/assets/img/2018/14.11.png)
![Import backed GPOs2]({{site.baseurl}}/assets/img/2018/14.12.png)

## 5.Export to Excel

![Export to Excel]({{site.baseurl}}/assets/img/2018/14.13.png)  

New excel file showed up, and I had to enable content on it</br>

![Export to Excel2]({{site.baseurl}}/assets/img/2018/14.14.png)  

Finally, I got all settings in excel for selected GPO  

![Export to Excel3]({{site.baseurl}}/assets/img/14.15.png)  

{% include advertising/gl-adsense.html %}