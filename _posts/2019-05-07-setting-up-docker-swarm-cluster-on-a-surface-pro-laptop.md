---
layout: post
title: "Setting up Docker Swarm cluster on a Surface Pro Laptop"
description: "It is possible to set up a lab on Windows 10 for Docker Swarm and use it to test some workloads, without missing on the road any of Surface Pro particularities..."
categories: [ powershell, Docker, Hyper-V ]
image: assets/img/2019/32.7.png
featured: false
author: eloy
comments: true 
---

Today, I wanted to use my Windows 10 Surface Pro Laptop as a platform to run Docker Swarm, just for testing purposes, not without some difficulties; here it's what I did:

## 1.Preparing network connections
2 network connections were needed at least, because I didn't want to lose internet connection at all on the laptop, and Docker Swarm required Hyper-V virtual switch, external type (that means 1 of the 2 network adapters…)

To achieve it, I had to enable both **Wifi** network adapter and **LAN** network adapter at the same time, devoting LAN network adapter to Docker Swarm exclusively

As I had only at hand one Apple USB to Ethernet adapter not in use, I tried to plug it to the Surface Pro laptop and set it up as the required LAN network adapter.

On the first attempt, it wasn't recognized properly, so I had to pull the wires and update the driver from Device Manager

![Preparing connections 1]({{site.baseurl}}/assets/img/2019/32.1.png)
![Preparing connections 2]({{site.baseurl}}/assets/img/2019/32.2.png)
![Preparing connections 3]({{site.baseurl}}/assets/img/2019/32.3.png)

{% include advertising/gl-adsense.html %}

## 2.Setting up Hyper-V Virtual Switch
I defined a new Hyper-V Virtual Switch from Hyper-V console this way:
![Hyper-V Switch]({{site.baseurl}}/assets/img/2019/32.4.png)


## 3.Enabling both LAN and Wifi connections
For this purpose, I had to create new registry value; of course I did prefer to use PowerShell for it:
```powershell
$regPath = HKLM:\\Software\Policies\Microsoft\Windows\WcmSvc\GroupPolicy
New-Item -Path $regPath -Force | Out-Null
New-ItemProperty -Path $regPath -Name fMinimizeConnections -Value 0 -Type DWORD -Force | Out-Null
```
![Enabling LAN and Wifi]({{site.baseurl}}/assets/img/2019/32.5.png)

Just added it, and I was able to see 2 network connections fantastic icons on the taskbar at the same time:

![Checking icons]({{site.baseurl}}/assets/img/2019/32.6.png)

{% include advertising/gl-adsense.html %}

## 4.Creating first Docker container
This step took a bit, but finally, it came out without difficulties…

On **Command Prompt** console with a single line, using Hyper-V driver for docker, I write down
```bash
docker-machine create -d hyperv manager
```

![Creating Docker container]({{site.baseurl}}/assets/img/2019/32.7.png)

Lastly, just to check docker machine is running as supposed to be:
```bash
docker-machine ls
```

![Checking docker container]({{site.baseurl}}/assets/img/2019/32.8.png)

Happy to have first docker machine on board!!

## 5.Conclusion
It is possible to set up a lab on **Windows 10 Surface Pro Laptop** to test **Docker Swarm**, without losing on the road any of **Surface Pro** particularities…
