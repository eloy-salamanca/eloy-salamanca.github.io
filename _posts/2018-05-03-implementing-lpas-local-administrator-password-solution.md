---
layout: post
title: "Implementing LPAS Local Administrator Password Solution"
description: "How to implement random Local Administrator Password Solution"
categories: [ Security, Active Directory, LAPS, Passwords ]
image: assets/img/2018/19.1.png
featured: false
author: eloy
comments: true
---

Local admin passwords have been a real headache over the time for big environments, because if only one user knows about it, potentially could take on control of all local computers.

It’s very typical to use a GPO and disable local admin account, but the drawback of this is whenever you run into problems with specific computers, like getting any of them out of the domain, you won’t count on “natural” methods to sort it out, and thus, will require time to solve or what’s even worst: give your powers to a far away end user, and back to square one

For above reasons, I set up and test LAPS (Local Administrator Password Solution) free Microsoft product, that comes with an elegant solution to this.

{% include advertising/gl-adsense.html %}

## LAPS - Introduction

Basically, what LPAS does is periodically randomizing local administrator passwords from every managed computer by installing a client-side component called **Group Policy Client Side Extension (CSE)**. This component will check the expiration of local Administrator account and will renew it when necessary, saving it into 2 specific confidential attributes of the computer object in Active Directory: **ms-Mcs-AdmPwd** and **ms-Mcs-AdmPwdExpirationTime**.
Following security measures are taken place in this process:

* Special access right is required to read the password, so only users with the given permission can read it from Active Directory.
* Transfer of password from managed computer to Active Directory is protected by Kerberos.
* Managed machine itself only has a permission to write the password to its own computer account, just in case it can be compromised.

**Group Policy Client Side Extension (CSE)** has following configurable parameters:
* Name of Administrator account (Administrator by default)
* Password Complexity (at least *1 character* in: capital letters, small letters, numbers and special characters by default)
* Password Length (*12 characters long by default*)
* Password maximum age (*30 days by default*)

## Setting up management computer

All components are contained in a single MSI package, downloadable here
First of all, I installed only Management Tools, which comes with the following:

* Fat client **AdmPwd.UI.exe** >> Provides the functionality of password retrieval for given computer and planned/inmediate password reset for a computer
* PowerShell **AdmPwd.PS** module, with following cmdlets:
    * AD schema extension
    * Delegation of permissions for:
        * Computer >> to be able to write passwords to Active Directory
        * IT staff >> read passwords and request password reset
* GPO Editor templates

![LPAS Setup]({{site.baseurl}}/assets/img/2018/19.1.png)

{% include advertising/gl-adsense.html %}

## Active Directory preparations

Next stage was to prepare Active Directory, to receive such local password administrator changes; specifically in **ms-Mcs-AdmPwd** and **ms-Mcs-AdmPwdExpirationTime** new attributes.
This was the process:

```powershell
Import-Module AdmPwd.PS
Update-AdmPwdADSchema
```

![Active Directory preparations]({{site.baseurl}}/assets/img/2018/19.2.png)

## Setting writing permissions for computer accounts

Used PowerShell module again to grant write permission on **ms-Mcs-AdmPwdExpirationTime** and **ms-Mcs-AdmPwd** on all computer accounts for SELF built-in account

```powershell
Import-Module AdmPwd.PS
Set-AdmPwdComputerSelfPermission -OrgUnit "Computers-OU"
```

![Setting writing permissions for computer accounts]({{site.baseurl}}/assets/img/19.3.png)

## Copying GPO templates

After that, as solution installs GPO templates, and because I use [centralized policy store for group policy administration](https://support.microsoft.com/en-us/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra){:target="_blank"}, I copied **admpwd.admx** GPO template and its corresponding language definitions file **admpwd.adml** from management PC to the PolicyDefinitions folder in one domain controller:

![Copying GPO templates1]({{site.baseurl}}/assets/img/2018/19.4.png)

![Copying GPO templates2]({{site.baseurl}}/assets/img/2018/19.5.png)

<br>
After that, LAPS is available throughout GPO, and only have to enable local admin password management; for now, default values are applied:
<br>

![Copying GPO templates3]({{site.baseurl}}/assets/img/2018/19.6.png)

{% include advertising/gl-adsense.html %}

## Setting up client computers

Time to activate **LAPS** on clients; as quickly and smoothly as possible; for example by scripting it

![Setting up client computers]({{site.baseurl}}/assets/img/2018/19.7.png)

After that and once upon policies refreshed, password and expiration time are inside computer object in Active Directory (I can see current values because my user account has Administrator privileges over the domain)

![Checking atributes]({{site.baseurl}}/assets/img/2018/19.8.png)

To see Password Expiration “human readable”, just only typed this:

```bash
w32tm /ntte "number you want to convert"
```

![Seeing password expiration]({{site.baseurl}}/assets/img/2018/19.9.png)

## Testing the solution

When a normal user tries to check out computer password, it's not possible at all

![Testing the solution]({{site.baseurl}}/assets/img/2018/19.10.png)

{% include advertising/gl-adsense.html %}