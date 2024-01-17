---
layout: post
title: "How to Install Vagrant and VirtualBox on Ubuntu from Windows Subsystem Linux"
description: "How to install Vagrant and VirtualBox on Ubuntu that comes with Windows 10, aka Windows Subsystem Linux"
categories: [ wsl, Linux, Vagrant, VirtualBox, Virtualization, ]
image: assets/img/2019/31.1.png
featured: false
author: eloy
comments: true
---

Lately, I am trying to learn more about [Ansible](https://www.ansible.com/){:target="_blank"} and [Vagrant](https://www.vagrantup.com/){:target="_blank"}, so first thing I need was to get all stuff installed on my computer ready. This is what I did:

## 1.Disable Hyper-V
To avoid unnecessary risks and for testin purposes, following **Vagrant** recommendations, I preferred to temporarily disable Hyper-V if you had enabled it, and forget about the battles of the hypervisors:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All
```

## 2.Installing Windows Subsystem for Linux (WSL)
This topic is pretty easy, only had to go to the **Microsoft Store** on Windows 10, and look for [Ubuntu App](https://www.microsoft.com/store/productId/9NBLGGH4MSV6){:target="_blank"}.

If you face any issue or want to know more info about this process, have a look at this link: 
[https://msdn.microsoft.com/en-us/commandline/wsl/install_guide](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide){:target="_blank"}

{% include advertising/gl-adsense.html %}

## 3.Installing VirtualBox
Just download it from [VirtualBox site](https://www.virtualbox.org/wiki/Downloads){:target="_blank"} and install it as usually

```bash
sudo apt install vagrant
```

![Installing Vagrant]({{site.baseurl}}/assets/img/2019/31.1.png)

Verifying Vagrant is installed correctly:
```bash
vagrant --version
```

![Checking for Vagrant version]({{site.baseurl}}/assets/img/2019/31.2.png)

## 5.Finishing last settings

### 5.1Environmental variables
This is absolutely necessary to avoid errors, you can add it to .bashrc if you prefers
```bash
export VAGRANT_WSL_ENABLE_WINDOWS_ACCESS="1"
```

### 5.2Creating Vagrantfile
`Vagrantfile` has to contain one line to avoid error related to Stderr, so including here how to download first image or box example to adjust related `Vagrantfile`

```bash
# Add new box
vagrant box add ubuntu/bionic64

# Make project folder and modifying Vagranfile
mkdir vagrant
cd vagrant
vagrant init ubuntu/bionic64
```

{% include advertising/gl-adsense.html %}

### 5.3Setting up Vagrantfile
As VirtualBox installed is Windows based, it’s necessary to share a folder for using by vagrant (WSL) and VirtualBox (Windows).

In my case, I drop it under `c:\vagrant`, so I moved `Vagrantfile` to this folder once created and modified it accordingly:
```bash
cp Vagrantfile /mnt/c/vagrant
cd /mnt/c/vagrant
```

Following lines were modified/added:
```bash
config.vm.synced_folder "../data", /mnt/c/vagrant"
```

```bash
config.vm.provider "virtualbox" do |vb|
  vb.customize [ "modifyvm", :id, "--uartmode1", "disconnected" ]
end
```

## 6.Running the box
Last and not least, this line to finish all good job done
```bash
vagrant up
```
{% include advertising/gl-adsense.html %}