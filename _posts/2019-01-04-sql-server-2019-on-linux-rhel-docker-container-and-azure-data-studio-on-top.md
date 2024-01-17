---
layout: post
title: "SQL Server 2019 on Linux RHEL Docker container and Azure Data Studio on top"
description: "How to install SQL Server 2019 on Linux RHEL Docker Container and accessing from Azure Data Studio"
categories: [ docker, PowerShell, Docker, SQL Server ]
image: assets/img/2019/27.3.png
featured: false
author: eloy
comments: true 
---

Having SQL Server on a Docker container is one of the best ideas I discovered; not only to keep my computer always clean, but also for performance and the ability to start again and again from scratch my testings; very very easy and fast.

This time I wanted to test SQL Server 2019 on Linux Red Hat; the results didn't disappoint me at all. Also, I tested [Azure Data Studio](https://docs.microsoft.com/en-us/sql/azure-data-studio/what-is?view=sql-server-2017){:target="_blank"}; a cross-platform database tool; previously released as the preview name SQL Server Studio

Let's see how was the process:

## Prerequisites to run SQL Server image on Docker
* Docker Engine 1.8+ on any supported Linux distribution or Docker for Mac/Windows. For more information, see Install Docker.
* Docker **overlay2** storage driver. This is the default for most users. If you find that you are not using this storage provider and need to change, please see the instructions and warnings in the docker documentation for configuring overlay2.
* Minimum of *2GB* of **disk** space.
* Minimum of *2GB* of **RAM**.

{% include advertising/gl-adsense.html %}

## Preparing the environment

### Downloading Docker image
Once Docker up and running at my system, I downloaded docker image:
```bash
docker pull mcr.microsoft.com/mssql/rhel/server:2019-CTP2.2
```

![Downloading Docker image]({{site.baseurl}}/assets/img/2019/27.1.png)

### Running RHEL Docker
After downloaded, started a container
```bash
docker run -e "ACCEPT_EULA=Y" -e "PASSWORD=Passw0rd" -p 1433:1433 --name sql01 -d mcr.microsoft.com/mssql/rhel/server:2019-CTP2.2
```

![Running RHEL Docker]({{site.baseurl}}/assets/img/2019/27.2.png)

Let's check everything is Ok, into the container:
```bash
docker exec -it sql01 "bash"
cat /etc/os-release
ls -la
```

![Check the container]({{site.baseurl}}/assets/img/2019/27.3.png)

Create new directory in the container, just to hold the db
```bash
mkdir /var/opt/mssql/backup
```

![Creating new directory]({{site.baseurl}}/assets/img/2019/27.4.png)

{% include advertising/gl-adsense.html %}

### Downloaded test db to play with
Just for testing, I downloaded from PowerShell test db

```bash
curl -OutFile "wwi.bak" https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Full.bak
```

![Downloaded test db]({{site.baseurl}}/assets/img/2019/27.5.png)

Copied it into RHEL Docker container:

```bash
docker cp .\wwi.bak sql01:/var/opt/mssql/backup
```

db restored

```bash
/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "Passw0rd" -Q "RESTORE FILELISTONLY FROM DISK = '/var/opt/mssql/backup/wwi.bak'"
```

![db restored]({{site.baseurl}}/assets/img/2019/27.6.png)

And after restored backup file, import data was needed

```bash
docker exec -it sql01 /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong!Passw0rd>" -Q "RESTORE DATABASE WideWorldImporters FROM DISK = '/var/opt/mssql/backup/wwi.bak' WITH MOVE 'WWI_Primary' TO '/var/opt/mssql/data/WideWorldImporters.mdf', MOVE 'WWI_UserData' TO '/var/opt/mssql/data/WideWorldImporters_userdata.ndf', MOVE 'WWI_Log' TO '/var/opt/mssql/data/WideWorldImporters.ldf', MOVE 'WWI_InMemory_Data_1' TO '/var/opt/mssql/data/WideWorldImporters_InMemory_Data_1'"
```

![imported data]({{site.baseurl}}/assets/img/2019/27.7.png)

{% include advertising/gl-adsense.html %}

## Azure Data Studio
After downloaded [Azure Data Studio](https://docs.microsoft.com/en-us/sql/azure-data-studio/what-is?view=sql-server-2017){:target="_blank"} (I do prefer the "zip" version, as I can leave it in the cloud), everything was accesible

![Azure Data Studio main window]({{site.baseurl}}/assets/img/2019/27.8.png)

![Azure Data Studio databases]({{site.baseurl}}/assets/img/2019/27.9.png)

![Azure Data Studio querying example data]({{site.baseurl}}/assets/img/2019/27.10.png)

Nice and easy one!
