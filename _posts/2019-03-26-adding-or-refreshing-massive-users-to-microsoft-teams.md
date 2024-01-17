---
layout: post
title: "Adding or refreshing massive users to Microsoft Teams"
description: "How to add a bunch of users to specific Microsoft Teams easily"
categories: [ Teams, PowerShell, Active Directory, Microsoft 365, Microsoft Teams, Office365 ]
image: assets/img/2019/28.1.png
featured: false
author: eloy
comments: true
---

Lately, I had to add around 1,000 users to a Microsoft Teams at once, and after that, on a second wave, I had to refresh it with another bunch of users.

As I didn't want to lose my finger on the attempt putting them one by one, I appealed PowerShell to come to the rescue...:-) here it is:

## Adding users massively to specific Team

### Create and add users to a Distribution List
After Distribution List created, I get use of powershell to craft `users.csv` file; this format:

```
login
LOGIN1@example.net
LOGIN2@example.net
LOGIN3@example.net
```

Then, I easily add all users using this snippet PowerShell code:
```powershell
Import-Module ActiveDirectory

$teamGroup = "Example-DL"

Import-Csv ".\users.csv" | foreach {
    Add-ADGroupMember -Identity $teamGroup -Members $_.login
    Write-Host $_.login
}
```

{% include advertising/gl-adsense.html %}

To check everything was as expected, I run following line to see total number of users:

```powershell
(Get-ADGroupMember -Identity Example-DL).count
```

After replication time passed to refresh changes in Office 365, I connected to Microsoft Online and check again number of users from this perspective; it will be what Microsoft Teams will only see:

```powershell
(Get-DistributionGroupMember -Identity Example-DL@example.net).count
```

### Add Distribution List users to the Team
I went to Microsoft Teams, clicked on the Team Name, and got access to the following screen:

![Adding members to the Team]({{site.baseurl}}/assets/img/2019/28.1.png)

Clicked on `Add member` button, and got access to the next dialog, putting the corresponding Distributed List created previously. Only a few moment to reprocess objects, and finally it is done!

![Including Distribution List members]({{site.baseurl}}/assets/img/2019/28.2.png)

{% include advertising/gl-adsense.html %}

### Refreshing members list
Simply had to update previously Distributed List, adding or removing users, and after that, repeat again the previous step; again using the same Distributed List.

Microsoft Teams will recognize already subscribed users and will add new ones only:

![Refreshing members list]({{site.baseurl}}/assets/img/2019/28.3.png)
