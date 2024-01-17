---
layout: post
comments: true
title: Enabling Remote PowerShell from Script on earlier and new PowerShell versions
excerpt: How to enable PowerShell remotely to run scripts
date: 2017-12-19 10:27:00 +0300
img: 37.9.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
category: nanoserver
tags: [PowerShell, nanoserver, Cluster] # add tag
---

Most efficient way to set up Hyper-V cluster is using NanoServer, because you will need lower memory and storage requirements, minimal downtime, and less maintenance; so this time I’m going to create basic Hyper-V Cluster consisting on 2 nodes to see how it works.

It’s important not making mistakes in any step, otherwise, you will have to start again from scratch

* After Windows 2016 installation media mounted (you can get it [here](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016) for evaluation purposes), you need to Import **NanoServerImageGenerator.psm1** module:

```powershell
Import-Module d:\NanoServer\NanoServerImageGenerator\NanoServerImageGenerator.psm1 -verbose
```


![Import Module]({{site.baseurl}}/assets/img/37.1.png)

* Setting up Administrator password:
```powershell
read-host -prompt "Enter password to be encrypted in mypass.txt" -assecurestring | convertfrom-securestring | out-file '.\mypass.txt'
$pass = cat '.\mypass.txt' | convertto-securestring
$nano01cred = new-object -typename System.Management.Automation.PSCredential -argumentlist "Administrator@nano01",$pass
$nano02cred = new-object -typename System.Management.Automation.PSCredential -argumentlist "Administrator@nano01",$pass
```

![Administrator password]({{site.baseurl}}/assets/img/37.2.png)

## Virtual Switch

Creating New Hyper-V Virtual Switch:
```powershell
New-VMSwitch -SwitchName "vSwitch" -SwitchType Internal
```

Configuring NAT Gateway IP Address:
```powershell
New-NetIPAddress –IPAddress 192.168.250.1 -PrefixLength 24 -InterfaceAlias "vEthernet"
```

Configuring NAT rule:
```powershell
New-NetNat –Name MyNATnetwork –InternalIPInterfaceAddressPrefix 192.168.250.0/24
```

![NAT rule]({{site.baseurl}}/assets/img/37.3.png)

## Images
```powershell
new-nanoserverimage -deploymenttype guest -edition standard -mediapath 'd:\' -basepath 'C:\_Apps\NanoServer\BasePath' -targetpath 'C:\Hyper-V\NANO01\NANO01.vhdx' -AdministratorPassword $pass -computername nano01 -compute -clustering -enableremotemanagementport -Package Microsoft-NanoServer-DNS-Package
new-nanoserverimage -deploymenttype guest -edition standard -mediapath 'd:\' -basepath 'C:\_Apps\NanoServer\BasePath' -targetpath 'C:\Hyper-V\NANO02\NANO02.vhdx' -AdministratorPassword $pass -computername nano02 -compute -clustering -enableremotemanagementport -Package Microsoft-NanoServer-DNS-Package
```

![Creating images]({{site.baseurl}}/assets/img/37.4.png)

* Creating 2 VMs based on above vhdx disks, turning 2 processors each:
```powershell
new-vm -name nano01 -memorystartupbytes 4gb -bootdevice vhd -vhdpath "C:\Hyper-V\NANO01\NANO01.vhdx" -path "C:\Hyper-V\NANO01\" -generation 2 -switch vswitch
set-vmprocessor -vmname nano01 -count 2
set-vm -vmname nano01 -automaticstopaction shutdown
enable-vmintegrationservice nano01 -name "Guest Service Interface"
new-vm -name nano02 -memorystartupbytes 4gb -bootdevice vhd -vhdpath "C:\Hyper-V\NANO02\NANO02.vhdx" -path "C:\Hyper-V\NANO02\" -generation 2 -switch vswitch
set-vmprocessor -vmname nano02 -count 2
set-vm -vmname nano01 -automaticstopaction shutdown
enable-vmintegrationservice nano01 -name "Guest Service Interface"
```

![Creating VMs]({{site.baseurl}}/assets/img/37.5.png)

* Starting and setting network interfaces from Nano Servers:
```powershell
Start-VM -Name nano01 | Out-Null
Invoke-Command -VMName nano01 -Credential $nano01cred -ScriptBlock {
   #Firewall
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Profile Any -Enabled True -Direction Inbound -Action Allow 
   Set-NetFirewallRule -DisplayName "Failover Clusters (UDP-In)" -Profile Any -Enabled True -Direction Inbound -Action Allow
   Set-NetFirewallRule -DisplayName "Failover Clusters (TCP-In)" -Profile Any -Enabled True -Direction Inbound -Action Allow

$nicname = (get-netadapter).name
get-netadapter -name $nicname | rename-netadapter -newname "NIC1"
New-NetIPAddress -InterfaceAlias "NIC1" -IPAddress "192.168.250.1" -AddressFamily IPv4 -PrefixLength 24 -DefaultGateway "192.168.250.3"
}
Start-VM -Name nano02 | Out-Null
Invoke-Command -VMName nano02 -Credential $nano02cred -ScriptBlock {

#Firewall
Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Profile Any -Enabled True -Direction Inbound -Action Allow
Set-NetFirewallRule -DisplayName "Failover Clusters (UDP-In)" -Profile Any -Enabled True -Direction Inbound -Action Allow
Set-NetFirewallRule -DisplayName "Failover Clusters (TCP-In)" -Profile Any -Enabled True -Direction Inbound -Action Allow

$nicname = (get-netadapter).name
get-netadapter -name $nicname | rename-netadapter -newname "NIC2"
New-NetIPAddress -InterfaceAlias "NIC2" -IPAddress "192.168.250.2" -AddressFamily IPv4 -PrefixLength 24 -DefaultGateway "192.168.250.3"
}
```

## Networking

![Getting results]({{site.baseurl}}/assets/img/37.6.png)

* Adding Cluster interface
```powershell
add-vmnetworkadapter -vmname nano01 -switchname vswitch -name "Cluster" -passthru | set-vmnetworkadapter -devicenaming on
add-vmnetworkadapter -vmname nano02 -switchname vswitch -name "Cluster" -passthru | set-vmnetworkadapter -devicenaming on
```

![Adding Cluster interface]({{site.baseurl}}/assets/img/37.7.png)

* Setting up IP Address
```powershell
Invoke-Command -VMName nano01 -Credential $nano01cred -ScriptBlock {
    #Configure Cluster Interface
    $nics = (get-netadapter).name
    foreach ($nic in $nics) {
        if (!($nicn -eq "NIC1")) {
            get-netadapter -name $nic | rename-netadapter -newname "CLUSTER"
        }
    }
    new-netipaddress -interfacealias Cluster -ipaddress "1.1.1.1" -AddressFamily IPv4 -PrefixLength 24
}
Invoke-Command -VMName nano02 -Credential $nano02cred -ScriptBlock {
    #Configure Cluster Interface
    $nics = (get-netadapter).name
    foreach ($nic in $nics) {
        if (!($nicn -eq "NIC2")) {
            get-netadapter -name $nic | rename-netadapter -newname "CLUSTER"
        }
    }
    new-netipaddress -interfacealias Cluster -ipaddress "1.1.1.2" -AddressFamily IPv4 -PrefixLength 24
}
```

Output should be something like this:

![IP Address]({{site.baseurl}}/assets/img/37.8.png)


## DNS Server

* Adding and configuring DNS Server role
```powershell
enter-pssession -vmname nano01 -credential $nano01cred
enable-windowsoptionalfeature -online -featurename dns-server-full-role
import-module dnsserver
get-command -module dnsserver
```

![DNS Server role]({{site.baseurl}}/assets/img/37.9.png)

* Creating DNS Zone:
```powershell
add-dnsserverprimaryzone -zonename contoso.com -zonefile contoso.com.dns
add-dnsserverresourcerecorda -name nano01 -zonename contoso.com -ipv4address 192.168.250.1
add-dnsserverresourcerecorda -name nano02 -zonename contoso.com -ipv4address 192.168.250.2
add-dnsserverresourcerecorda -name cluster -zonename contoso.com -ipv4address 192.168.250.115
get-dnsserverresourcerecord -zonename "contoso.com"
```

![DNS Zone]({{site.baseurl}}/assets/img/37.10.png)


## Hyper-V Cluster
```powershell
Invoke-Command -VMName nano01 -Credential $nano01cred -ScriptBlock {
   $CLName = "CLHV"
   $CLIpAddress = "192.168.250.115"
   New-Cluster -Name $CLName -Node nano01,nano02 -StaticAddress $CLIpAddress -NoStorage -Verbose
}
```
