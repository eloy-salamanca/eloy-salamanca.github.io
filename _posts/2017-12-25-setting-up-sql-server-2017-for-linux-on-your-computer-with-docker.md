---
layout: post
title: "Setting up SQL Server 2017 for linux on your computer with Docker"
description: "SQL Server 2017 has released new free linux image for Docker, appealing for local development testings"
categories: [ docker, databases, SQL Server ]
image: assets/img/2017/9.7.png
featured: false
author: eloy
comments: true
---
Frequently, I need to set up lab environment and push/pull data from/to a SQL Server db, but I didn't want to use an external one, but my computer (to work on the go), nor I didn't feel like to install a full heavy SQL Server installation (including its required tons of components), so lately I've been testing SQL Server 2017 with Docker, and results were more spectaculars than I thought in the beginning.

This is a brief introduction on how to accomplish this (very very easy and clean); surely you can imagine applications for your daily work.

## Hyper-v

As Docker images run over Windows Containers, I needed to turn Hyper-V feature on, all available settings. After reset, I was able to go to next step.

![Setting Windows Features]({{site.baseurl}}/assets/img/2017/9.1.png)

{% include advertising/gl-adsense.html %}

## Install Docker

(Requires Microsoft Windows 10 Professional or Enterprise 64-bit). After that, I restarted my computer again.

![Installed Docker]({{site.baseurl}}/assets/img/2017/9.2.png)

Docker was now started properly:

![Docker started]({{site.baseurl}}/assets/img/2017/9.3.png)


## Configuring Docker settings

For SQL Server to run specifically, I needed to touch some settings: sharing my c: drive to save image changes and increase RAM Memory up to 4GB for SQL Server to start.

![Docker settings menu]({{site.baseurl}}/assets/img/2017/9.4.png)

![Docker shared drives]({{site.baseurl}}/assets/img/2017/9.5.png)

![Docker advanced settings]({{site.baseurl}}/assets/img/2017/9.6.png)

{% include advertising/gl-adsense.html %}

## Getting SQL Server 2017 for linux image

Once Docker is running, I pulled down SQL Server 2017 on linux image.

```bash
docker pull microsoft/mssql-server-linux:2017-latest
```

![SQL Server 2017 docker image]({{site.baseurl}}/assets/img/2017/9.7.png)

Checked that new image is available for Docker to run, started it, and checked again from Docker that everything is running fine:

```bash
docker images
docker run -e "ACCEPT EULA=Y" -e "MSSQL SA PASSWORD=Pa$$w0rd" --name "sql01" -p 1433:1433 -v sql01data:/var/opt/mssql -d microsoft/mssql-server-linux:2017-latest
docker ps -a
```

![SQL Server 2017 docker image downloaded]({{site.baseurl}}/assets/img/2017/9.8.png)

Then, used SQL Operations Studio to test everything is OK (if you want some details about this, just check [this post](link)

![SQL Server Studio]({{site.baseurl}}/assets/img/2017/9.9.png)

{% include advertising/gl-adsense.html %}