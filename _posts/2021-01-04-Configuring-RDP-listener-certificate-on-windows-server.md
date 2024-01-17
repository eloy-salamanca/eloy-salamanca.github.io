---
layout: post
title: "Configuring Remote Desktop listener certificate on Windows Server"
description: "How to set Remote Desktop listener certificate to proper internal CA compatible certificate on a Windows Server, domain joined"
categories: [certificates, Windows, Security]
image: assets/img/2021/50.4.png
featured: false
author: eloy
comments: true
---
 

For security reasons, I had to force the use of certificates delivered by our internal CA on RDP connections, and it was more a pretty straight forward process than I though in the beginning.

## Requesting SSL certificate to the internal CA for Remote Desktop Authentication purposes

First of all, I needed to make a request for the new certificate from the intended Windows Server:
![Requesting the certificate to the internal CA]({{site.baseurl}}/assets/img/2021/50.1.png)

As I had templates deployed in the Active Directory for Remote Desktop Authentication purposes, I picked that up as enrollment policy
![Active Directory enrollment policy]({{site.baseurl}}/assets/img/2021/50.2.png)

Selected previously defined certificate template (only for Remote Desktop Authentication purposes) and click on **Properties** to define Common and Alternative name
![Properties on the certificate's template]({{site.baseurl}}/assets/img/2021/50.4.png)

To verify everything worked as expected, pressed **Start** > **Run** and typed `certlm.msc`
A new nice SSL certificate, CA internal compatible, had been released.
![opening certlm.msc]({{site.baseurl}}/assets/img/2021/50.6.png)
![Checking for the new certificate]({{site.baseurl}}/assets/img/2021/50.7.png)

{% include advertising/gl-adsense.html %}

## Pointing Remote Desktop listener to the new certificate

Last and not least, I had to move the Remote Desktop listener to the new certificate (easy peasy), and as I like powershell above all, I decided to carry on everything using it

### Get Digital Thumbprint of the certificate
Started Powershell and typed the following: `Get-ChildItem cert:\LocalMachine\My`
Copied the Thumbprint
![getting the Digital Thumbprint]({{site.baseurl}}/assets/img/2021/50.8.png)

### Modify the Remote Desktop listener
Using that thumbprint, rolled out the following, substituting appropiate Thumbprint
```
wmic /namespace:\\root\cimv2\TerminalServices PATH Win32_TSGeneralSetting Set SSLCertificateSHA1Hash="THUMBPRINT"
```
![Modifying Remote Desktop Listener]({{site.baseurl}}/assets/img/2021/50.9.png)

{% include advertising/gl-adsense.html %}

## Testing new behaviour
First of all, you will need to delete historic session to the server you will like to test; to do so, simply open `regedit` and go to `HKEY_CURRENT_USER\Software\Microsoft\Terminal Server Client\Servers\`; after that delete the entire folder related to the server or IP.

Once you have done this, simply open new RDP to the intended server you want and shouldn't receive any warning for incoming new connection anymore, because your computer trust on it.
