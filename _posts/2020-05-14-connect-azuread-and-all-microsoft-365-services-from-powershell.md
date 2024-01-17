---
layout: post
title: "Connect Azure AD and all Microsoft 365 Services from PowerShell"
description: "How to connect to Microsoft 365 Services from single PowerShell windows"
categories: [ powershell, Office365, Microsoft365, ExchangeOnline, SharePointOnline, Teams, AzureAD ]
image: assets/img/2020/45.1.png
featured: false
author: eloy
comments: true
---

## Pre-requisites
* Your account must be member of the corresponding Microsoft 365 admin role you're going to administer.
* Your computer must use 64-bit version of latest Windows releases, at a minimum:
    - Windows 7 SP1 (with .NET Framework 4.5.x and 3.0 or 4.0)
    - Windows Server 2008 R2 SP1 (with .NET Framework 4.5.x and 3.0 or 4.0)
* Have installed following Modules
    - [Azure Active Directory V2](https://docs.microsoft.com/en-us/office365/enterprise/powershell/connect-to-office-365-powershell##connect-with-the-azure-active-directory-powershell-for-graph-module)
    - [SharePoint Online Management Shell](https://go.microsoft.com/fwlink/p/?LinkId=255251)
    - [Skype for Business Online, Windows Powershell Module](https://go.microsoft.com/fwlink/p/?LinkId=532439)
    - [Exchange Online PowerShell V2](https://docs.microsoft.com/powershell/exchange/exchange-online/exchange-online-powershell-v2/exchange-online-powershell-v2?view=exchange-ps#install-and-maintain-the-exchange-online-powershell-v2-module)
    - [Teams PowerShell Overview](https://docs.microsoft.com/microsoftteams/teams-powershell-overview)
* Allow running signed scripts in your system
```powershell
Set-ExecutionPolicy RemoteSigned
```

{% include advertising/gl-adsense.html %}

## Using a Password
```powershell
$credential = Get-Credential
```
### Azure Active Directory PowerShell for Graph Module
```powershell
Connect-AzureAD -Credential $credential
```
### Azure Active Directory Module for Windows PowerShell
```powershell
Connect-MsolService -Credential $credential
```
### SharePoint Online
```powershell
Import-Module Microsoft.Online.SharePoint.PowerShell -DisableNameChecking
Connect-SPOService -Url https://<domainhost>-admin.sharepoint.com -credential $credential
```
### Skype for Business Online
```powershell
Import-Module SkypeOnlineConnector
$sfboSession = New-CsOnlineSession -Credential $credential
Import-PSSession $sfboSession
```
### Exchange Online
```powershell
Connect-ExchangeOnline -Credential $credential -ShowProgress $true
```
### Teams
```powershell
Import-Module MicrosoftTeams
Connect-MicrosoftTeams -Credential $credential
```
### Microsoft 365 Security & Compliance Center
```powershell

$SccSession = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri https://ps.compliance.protection.outlook.com/powershell-liveid/ -Credential $credential -Authentication "Basic" -AllowRedirection
Import-PSSession $SccSession -Prefix cc
```
### Close Down all connected sessions
```powershell
Remove-PSSession $sfboSession ; Remove-PSSession $SccSession ; Disconnect-SPOService ; Disconnect-MicrosoftTeams
```

{% include advertising/gl-adsense.html %}

### AzureAD and all Microsoft365 from Azure Active Directory PowerShell for Graph module 
```powershell
$orgName="<for example, litwareinc for litwareinc.onmicrosoft.com>"
$credential = Get-Credential
Connect-AzureAD -Credential $credential
Import-Module Microsoft.Online.SharePoint.PowerShell -DisableNameChecking
Connect-SPOService -Url https://$orgName-admin.sharepoint.com -credential $credential
Import-Module SkypeOnlineConnector
$sfboSession = New-CsOnlineSession -Credential $credential
Import-PSSession $sfboSession
$SccSession = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri https://ps.compliance.protection.outlook.com/powershell-liveid/ -Credential $credential -Authentication "Basic" -AllowRedirection
Import-PSSession $SccSession -Prefix cc
Connect-ExchangeOnline -Credential $credential -ShowProgress $true
Import-Module MicrosoftTeams
Connect-MicrosoftTeams -Credential $credential
```

### AzureAD and all Microsoft365 from Azure Active Directory Module for Windows PowerShell module
```
$orgName="<for example, litwareinc for litwareinc.onmicrosoft.com>"
$credential = Get-Credential
Connect-MsolService -Credential $credential
Import-Module Microsoft.Online.SharePoint.PowerShell -DisableNameChecking
Connect-SPOService -Url https://$orgName-admin.sharepoint.com -credential $credential
Import-Module SkypeOnlineConnector
$sfboSession = New-CsOnlineSession -Credential $credential
Import-PSSession $sfboSession
$SccSession = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri https://ps.compliance.protection.outlook.com/powershell-liveid/ -Credential $credential -Authentication "Basic" -AllowRedirection
Import-PSSession $SccSession -Prefix cc
Connect-ExchangeOnline -Credential $credential -ShowProgress $true
Import-Module MicrosoftTeams
Connect-MicrosoftTeams -Credential $credential
```

{% include advertising/gl-adsense.html %}

## Using Modern Authentication (MFA)
Single block to connect to **Azure AD**, **SharePoint Online**, **Skype for Business**, **Exchange Online**, and **Teams**

```powershell
$acctName="<UPN of the account, such as belindan@litwareinc.onmicrosoft.com>"
$orgName="<for example, litwareinc for litwareinc.onmicrosoft.com>"
```
### Azure Active Directory PowerShell for Graph Module
```powershell
Connect-AzureAD
```
### Azure Active Directory Module for Windows PowerShell
```powershell
Connect-MsolService
```
### SharePoint Online
```powershell
Connect-SPOService -Url https://$orgName-admin.sharepoint.com
```
### Skype for Business Online
```powershell
$sfboSession = New-CsOnlineSession -UserName $acctName
Import-PSSession $sfboSession
```
### Exchange Online
```powershell
Connect-ExchangeOnline -UserPrincipalName $acctName -ShowProgress $true
```
### Teams
```powershell
Import-Module MicrosoftTeams
Connect-MicrosoftTeams
```

## AzureAD and all Microsoft365 from Azure Active Directory PowerShell for Graph module
```powershell
$acctName="<UPN of the account, such as belindan@litwareinc.onmicrosoft.com>"
$orgName="<for example, litwareinc for litwareinc.onmicrosoft.com>"
#Azure Active Directory
Connect-AzureAD
#SharePoint Online
Connect-SPOService -Url https://$orgName-admin.sharepoint.com
#Skype for Business Online
$sfboSession = New-CsOnlineSession -UserName $acctName
Import-PSSession $sfboSession
#Exchange Online
Connect-ExchangeOnline -UserPrincipalName $acctName -ShowProgress $true
#Teams
Import-Module MicrosoftTeams
Connect-MicrosoftTeams
```

{% include advertising/gl-adsense.html %}

### AzureAD and all Microsoft365 from Azure Active Directory Module for Windows PowerShell module
```powershell
$acctName="<UPN of the account, such as belindan@litwareinc.onmicrosoft.com>"
$orgName="<for example, litwareinc for litwareinc.onmicrosoft.com>"
#Azure Active Directory
Connect-MsolService
#SharePoint Online
Connect-SPOService -Url https://$orgName-admin.sharepoint.com
#Skype for Business Online
$sfboSession = New-CsOnlineSession -UserName $acctName
Import-PSSession $sfboSession
#Exchange Online
Connect-ExchangeOnline -UserPrincipalName $acctName -ShowProgress $true
#Teams
Import-Module MicrosoftTeams
Connect-MicrosoftTeams
```

**Note** > To connect to **Security & Compliance Center** with **Modern Authentication (MFA)**, you need to open **Exchange Online** console, then hit below buttom
![Security & Compliance Center with MFA]({{site.baseurl}}/assets/img/2020/45.2.png)

Source: [https://docs.microsoft.com/en-us/office365/enterprise/powershell/connect-to-all-office-365-services-in-a-single-windows-powershell-window](https://docs.microsoft.com/en-us/office365/enterprise/powershell/connect-to-all-office-365-services-in-a-single-windows-powershell-window){:target="_blank"}
