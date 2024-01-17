---
layout: post
title: "Enabling Remote PowerShell from Script on earlier and new PowerShell versions"
description: "How to enable PowerShell remotely to run scripts"
categories: [ powershell ]
image: assets/img/2018/11.1.png
featured: false
author: eloy
comments: true
---

Lately, I had to execute Powershell very simple script on a large list of Windows systems, but lot of them weren't ready for it, so I ended up having to do some hacking, and previously checking if the system was ready to accept external commands.

The most tricky thing was to check for availability of WinRM remotely, then being able to enable when necessary prior executing <strong>Invoke-Command</strong> from Powershell.

###  Checking WinRM remotely

First of all, [psexec, from PsTools](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec){:target="_blank"}  has to be installed.

After that, is it possible to try this:
```powershell
$WinRMEnabled = [bool](Test-WSMan -ComputerName $srv -ErrorAction SilentlyContinue)
If (!($WSManEnabled)) {
    .\PsExec.exe -h -u "user" -p "passwd" \\$server C:\Windows\System32\winrm.cmd quickconfig -quiet
}
```

Once WinRM is enabled, I had to unzip certain file over Powershell 2.0, not an easy task, but finally, I found out this very nice solution:

```powershell
Invoke-Command -ComputerName $srv -Credential $mycred -ScriptBlock {
   Function Expand-ZIPFile ($fileZIP, $unzipPATH) {
      $shell = new-object -com shell.application
      $zip = $shell.NameSpace($fileZIP)
      foreach($item in $zip.items()) {
         $shell.Namespace($unzipPATH).copyhere($item)
      }
   }
   Expand-ZipFile "C:\SoftwareBase\TCPdata\tcpvcon_ES1.zip" -unzipPATH "C:\SoftwareBase\TCPdata"
}
```
Goal achieved, WinRM full operative and file unzipped in the end!

![Enable Remote PowerShell]({{site.baseurl}}/assets/img/2018/11.1.png)

{% include advertising/gl-adsense.html %}