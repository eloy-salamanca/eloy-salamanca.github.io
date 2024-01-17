---
layout: post
title: "AzurePowerShell Replacing AzureRM with Az Module"
description: "Getting PowerShell toolset up to date by replacing AzureRM with Az Module"
categories: [ powershell ]
image: assets/img/2018/25.4.png
featured: false
author: eloy
comments: true 
---

In August 2018, a new Az PowerShell module was released, combining the functionality of the AzureRM and AzureRM.Netcore modules.

Az module was designed to work on both PowerShell 5.1 and PowerShell Core, so managing Azure resources will always be in sync and up to date no matter the platform you’re working on, latest Azure tooling in every PowerShell.

Another reason for this module to come up is that now Azure Cloud Shell runs PowerShell Core. This implies AzureRM is not compatible anymore.

For a full list of features and more details, check [GitHub announcement](https://github.com/Azure/azure-powershell/blob/preview/documentation/announcing-az-module.md){:target="_blank"}

If you want to use this Az module, it’s **imperative to remove all** AzureRM **modules before installing AZ**. I did it using this function from Microsoft you can check here: [Uninstall the Azure PowerShell module](https://docs.microsoft.com/en-us/powershell/azure/uninstall-azurerm-ps?view=azurermps-6.10.0){:target="_blank"}; let’s see the process I followed:

## Check for the current version of AzureRM module
As normally is constantly being updated, it was a good idea to check for the current version, this way:

```powershell
Get-Module AzureRM -List | Select-Object Name, Version, Path
```

{% include advertising/gl-adsense.html %}

![Check for the current AzureRM module version]({{site.baseurl}}/assets/img/2018/25.1.png)

## Uninstalling AzureRM module

```powershell
function Uninstall-AllModules {
  param(
    [Parameter(Mandatory=$true)]
    [string]$TargetModule,

[Parameter(Mandatory=$true)]
[string]$Version,

[switch]$Force
  )
  $AllModules = @()
  ‘Creating list of dependencies…’
  $target = Find-Module $TargetModule -RequiredVersion $version
  $target.Dependencies | ForEach-Object {
    $AllModules += New-Object -TypeName psobject -Property @{name=$.name; version=$.requiredversion}
  }
  $AllModules += New-Object -TypeName psobject -Property @{name=$TargetModule; version=$Version}
  foreach ($module in $AllModules) {
    Write-Host (‘Uninstalling {0} version {1}’ -f $module.name,$module.version)
    try {
      Uninstall-Module -Name $module.name -RequiredVersion $module.version -Force:$Force -ErrorAction Stop
    } catch {
      Write-Host (“`t” + $_.Exception.Message)
    }
  }
}
```

{% include advertising/gl-adsense.html %}

I simply copied above snippet into a PowerShell session to find out current version

![Check for the current version]({{site.baseurl}}/assets/img/2018/25.2.png)

Then, removed older version of AzureRM module using this function:

```powershell
Uninstall-AllModules -TargetModule AzureRM -Version 6.7.0 -Force
```
After that, I double checked that it was correctly uninstalled:

![Uninstall AzureRM old module]({{site.baseurl}}/assets/img/2018/25.3.png)

And finally, I was able to install Az module properly:

```powershell
Install-Module -Name Az -AllowClobber
```
![Installing Az module]({{site.baseurl}}/assets/img/2018/25.4.png)
