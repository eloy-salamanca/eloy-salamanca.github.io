---
layout: post
title: "PowerShell most common installers for admins"
description: "Last installers and modules to get your PowerShell toolset ready"
categories: [ powershell, Azure, Azure Stack, Docker, Exchange Online, Office365, SharePoint, Skype for Business ]
image: assets/img/2018/12.1.png
featured: false
author: eloy
comments: true 
---
Have you ever change computer and went mad trying to recap all required installers for your daily work?
Me too, so I’m trying to summarize here most commons installers that I usually need.
Feel free to add suggestions to this list, so it would become more useful!

## Getting the framework
If still not on win10, you need this: [Windows Management Framework 5.1](https://www.microsoft.com/en-us/download/details.aspx?id=54616){:target="_blank"} Last PowerShell version, essential to access PSGallery and install-modules

{% include advertising/gl-adsense.html %}

### Active Directory Service
Links to RSAT
* [Remote Server Administration Tools for Windows 10](https://www.microsoft.com/en-us/download/details.aspx?id=45520){:target="_blank"}
* [Remote Server Administration Tools (RSAT) for Windows 8.1](http://www.microsoft.com/download/details.aspx?id=39296){:target="_blank"}
* [Remote Server Administration Tools (RSAT) for Windows 8](http://www.microsoft.com/download/en/details.aspx?id=28972){:target="_blank"}
* [Remote Server Administration Tools (RSAT) for Windows 7 with SP1 (both x86 and x64)](http://www.microsoft.com/downloads/en/details.aspx?familyid=7d2f6ad7-656b-4313-a005-4e344e43997d){:target="_blank"}
* [Remote Server Administration Tools (RSAT) for Windows Vista 32-bit (x86)](http://www.microsoft.com/downloads/en/details.aspx?familyid=9ff6e897-23ce-4a36-b7fc-d52065de9960&amp;displaylang=en){:target="_blank"}
* [Remote Server Administration Tools (RSAT) for Windows Vista 64-bit (x64)](http://www.microsoft.com/downloads/en/details.aspx?familyid=d647a60b-63fd-4ac5-9243-bd3c497d2bc5){:target="_blank"}

If you're using **Windows 10 1809** at least and want to upgrade RSAT by using PowerShell, see [this link](https://eloysalamanca.es/powershell/install-windows-10-rsat-feature-by-using-powershell/){:target="_blank"}

### Trusting PowerShell running scripts:
After all necessary stuff installed, important to modify execution policy, to continue with the configuration of PowerShell:
```powershell
Set-ExecutionPolicy Unrestricted -Force
```

{% include advertising/gl-adsense.html %}

## PowerShell Package Management
**PowerShellGet 1.6.0**: PowerShell module with for discovering, installing, updating and publishing the PowerShell capabilities, like Modules, DSC Resources...
```powershell
Install-Module -Name PowerShellGet -Force -Verbose
```

To configure PowerShell Gallery as a trusted repository:
```powershell
Set-PSRepository -Name 'PSGallery' -InstallationPolicy Trusted
```

**PSReadline module**: To get syntax highlighting
```powershell
Install-Module PSReadline -Verbose
```

**NuGet Provider**: To work with PowerShell Gallery:
```powershell
Install-PackageProvider -Name NuGet -Verbose
```

**Docker**: To play Windows Containers:
```powershell
Install-Module -Name Docker -Verbose
```

**Chocolatey**: Hundred of useful packages, like SysInternals
```powershell
Register-PackageSource -Name chocolatey -ProviderName Chocolatey -Location http://chocolatey.org/api/v2/
```

**SysInternals**: One of must have; very easy with Package Management:
```powershell
Install-Package -Name Sysinternals
```

After that, I can use Sysinternals tools this default location:
![SysInternals tools]({{site.baseurl}}/assets/img/2018/12.2.png)

**Getting the list of current sources**:
```powershell
Get-PackageSource
```
![Package Source]({{site.baseurl}}/assets/img/2018/12.3.png)

{% include advertising/gl-adsense.html %}

## Azure

### Azure Rights Management
Tools to administer: [Azure Rights Management Administratio Tool](https://www.microsoft.com/en-us/download/details.aspx?id=30339){:target="_blank"}

### Azure most common modules
```powershell
Install-Module -Name Azure -Repository PSGallery -Force -Verbose
Install-Module -Name AzureAD -Verbose
Install-Module -Name Az -AllowClobber
Install-Module -Name AzureADPreview -Verbose
Install-Module -Name Microsoft.Azure.ActiveDirectory.PIM.PSModule -Verbose
Install-Module -Name xPSDesiredStateConfiguration -Verbose
```

PS: If later on you need to uninstall these modules, follow this line:
```powershell
Get-Module -ListAvailable | where-Object {$_.Name -like “Azure*”} | Uninstall-Module
```

### Azure Stack (up to 1.3.0)

#### Connected Scenario (Internet connectivity available)
If you're willing to play with Azure Stack, you're going to need this, it's necessary to allow subsequent installation of Azure Stack compatible AzureRM modules, since they are installed by leveraging API version profiles.
```powershell
Install-Module -Name AzureRm.BootStrapper
Use-AzureRmProfile -Profile 2017-03-09-profile -Force
```

PS: In case you receive any error messages at this point, open File Explorer, navigate to C:\Program Files\WindowsPowerShell\Modules and to C:\Users\Administrator\Documents\WindowsPowerShellModules, identify any folders starting with Azure, and retry running Use-AzureRmProfile cmdlet.
```powershell
# Install Module Version 1.3.0 if Azure Stack is running 1804 at minimum
Install-Module –Name AzureStack –RequiredVersion 1.3.0
# Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804
Install-Module -Name AzureStack -RequiredVersion 1.2.11
```

To confirm AzureRM and AzureStack available modules:
```powershell
Get-Module -ListAvailable | Where-Object Name -like 'Azure*'
```

{% include advertising/gl-adsense.html %}

#### Disconnected or partially connected scenario (limited Internet connectivity)

1. Sign in to a computer with internet connectivity and use following script to download AzureRM, AzureStack packages:

```powershell
$Path = ""

Save-Package -ProviderName NuGet `
  -Source https://www.powershellgallery.com/api/v2 -Name AzureRM `
  -Path $Path -Force `
  -RequiredVersion 1.2.11
  
Save-Package -ProviderName NuGet `
  -Source https://www.powershellgallery.com/api/v2 -Name AzureStack `
  -Path $Path -Force `
  -RequiredVersion 1.3.0
```

PS: If you are not running Azure Stack 1804, change **requiredversion** parameter to 1.2.11

2. Copy downloaded packages to USB device
3. Copy packages to the workstation
4. Register default repository

```powershell
$SourceLocation = "[local packages]"
$RepoName = "MyNuGetSource"
Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation `
-InstallationPolicy Trusted

Install-Module AzureRM -Repository $RepoName

Install-Module AzureStack -Repository $RepoName
```

### Azure Stack Tools
This has to be installed from GitHub
```powershell
Set-Location -Path 'C:\'
Invoke-WebRequest https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip
Expand-Archive master.zip -DestinationPath . -Force
Set-Location –Path C:\AzureStack-Tools-master
```

With these tools, you can identify MAS (Microsoft Azure Stack) capabilities, manage Azure Stack VM infrastructure and images, configuring Resource Manager policies, register MAS with Azure, MAS deployment, connectivity to MAS, MAS tenant management, and validation of MAS Resource Manager templates.

### Azure CLI 2.0
Only was necessary to install from this .MSI installer [https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest){:target="_blank"}

### AzureADPreview
The module offered in the Cloud Shell, it requires PowerShell 3.0 or above.
It’s possible to install it using PowerShellget:
```powershell
Install-Module AzureADPreview
```

{% include advertising/gl-adsense.html %}

## Windows Update
This module allow you to control Windows Update patching via PowerShell; easier than traditional way:
```powershell
Install-Module -Name PSWindowsUpdate -Verbose
```

---

## OpenSSH
With last Windows 10 release, Fall Creators Update (1709), an SSH client and server were included (for now on beta version):
```powershell
Get-WindowsCapability -Online | ? Name -like 'OpenSSH*'
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

---

## Office 365
To administer Office 365 and Azure Active Directory, it's required:
1. Make sure that at least **Microsoft .NET Framework 3.5** is installed and enabled (required for OS versions prior to Windows 10)
2. Install the 64-bit version of the [Microsoft Online Services Sign-In Assistant for IT Professionals RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950){:target="_blank"} > Access to Microsoft Online Services, yet not available in PSGallery
3. Install the Microsoft Azure Active Directory Module for Windows PowerShell:
```powershell
Install-Module MSOnline -Verbose
```

- If prompted to install the NuGet provider, say YES - If prompted to install the module from PSGallery, say YES - After installation, close the PowerShell command window

{% include advertising/gl-adsense.html %}

## Exchange Online
From Exchange Admin Center, go to Hybrid \ Setup, and download 'Exchange Online Remote PowerShell' (it supports multi-factor authentication). After that, proced to install it.

![Office 365 Panel]({{site.baseurl}}/assets/img/2018/12.4.png)

WinRM needs to allow basic authentication, to verify:
```powershell
winrm get winrm/config/client/auth
```

If you can’t see ‘Basic’ value, you need to enable:
```powershell
winrm set winrm/config/client/auth @{Basic="true"}
```

## SharePoint
Downloading this module to manage SharePoint Online: [SharePoint Online Module](https://www.microsoft.com/en-us/download/details.aspx?id=35588){:target="_blank"}

You can install it also via PSGallery:
**SharePoint Online**
```powershell
Install-Module SharePointPnPPowerShellOnline
```

**SharePoint 2016**
```powershell
Install-Module SharePointPnPPowerShell2016
```

**SharePoint 2013**
```powershell
Install-Module SharePointPnPPowerShell2013
```

**SharePoint setup-files**
You can download setup files from the [releases](https://github.com/sharepoint/pnp-powershell/releases){:target="_blank"} section of the PnP PowerShell repository

**Installation-script**
This is an alternative for installation on machines that have at least PowerShell v3 installed. 
```powershell
Invoke-Expression (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/sharepoint/PnP-PowerShell/master/Samples/Modules.Install/Install-SharePointPnPPowerShell.ps1'){:target="_blank"}
```

**Updating SharePoint Module**
```powershell
Update-Module SharePointPnPPowerShell*
```

{% include advertising/gl-adsense.html %}

## Skype for Business Online
Module for Skype for Business: [Skype for Business Online Module](https://www.microsoft.com/en-us/download/details.aspx?id=39366){:target="_blank"}

## Windows Virtual Desktop
Lately, Microsoft has created Windows Virtual Desktop (WVD), in an attempt to substitute traditional Remote Desktop Services (RDS). To configure WVD Service, this PowerShell module is necessary:
```powershell
Install-Module -Name Microsoft.RDInfra.RDPowershell
```

## Update
Last, but not least, advisable to update help and all Modules:
```powershell
Update-Help
Update-Module
```
