---
layot: post
title: "Patching Linux Servers not connected to internet"
description: "How to patch linux servers in the company when are not connected to internet"
categories: [ squid, security, linux, squid, wsus, patching]
image: assets/img/2022/51.3.png
featured: false
author: eloy
comments: true 
---

I didn’t want to open internet to a bunch Linux Servers, so I wanted to use one and only as a cache and make it a single spot to get all updated, thought in Squid and worked smoothly...

## Squid Proxy Server

### Installing Squid

I chose an internet-connected Ubuntu Linux 20.4 Server, only used by IT staff, where I installed Squid proxy
```bash
sudo apt update
sudo apt install squid
```

Checking that Squid was alive:

```bash
netstat -plunt | grep 3128
```

![Testing Squid]({{site.baseurl}}/assets/img/2022/51.1.png)

{% include advertising/gl-adsense.html %}

### Configuring Squid port

Made a backup copy of the configuration file just in case
```bash
sudo cp /etc/squid/squid.conf{,.ori}
```

By default, Squid is set to listen on port `3128` on all network interfaces, but I preferred to change it to `8080`.

Also, access from remote host on the local network must be specific (allowed remoted hosts)

```bash
sudo nano /etc/squid/squid.conf
```

![Setting up Squid port]({{site.baseurl}}/assets/img/2022/51.2.5.png)

### Allowing connections only from specific Linux hosts

Better to prevent intruders, so I created new file with intended IP Addresses:

```bash
sudo nano /etc/squid/allowed_hosts.txt
```

Put inside allowed hosts only  
`host1`  
`host2`  
`...`  

Created new ACL in `squid.conf` to allow access for allowed hosts only.  

![Restricting connections]({{site.baseurl}}/assets/img/2022/51.3.png)

![Restricting connections]({{site.baseurl}}/assets/img/2022/51.4.png)

And restarted Squid
```bash
systemctl restart squid
```

Finally, important to allow corporate firewalls connections `TCP/8080` from allowed hosts if necessary

---

## Clients

On each host, did the following (Linux Ubuntu Server 20.4)
```bash
sudo nano /etc/apt/apt.conf.d/05proxy
```

Added following lines
```bash
Acquire {
  HTTP::proxy "http://[IP_ADDRESS_SQUID]:8080";
  HTTPS::proxy "https://[IP_ADDRESS_SQUID]:8080";
}
```

From this moment on, I was able to run `apt update` and `apt upgrade` to receive their updates from the internet.

{% include advertising/gl-adsense.html %}
