---
title: "Checking Availability for internal URLs"
description: "Health modeling and Observability of Mission-Critical workloads"
categories: [ monitoring ]
image: assets/img/2024/63.0.jpg
featured: true
author: eloy
comments: true
---

Options check for availability of internal resources

Header image by <a href="https://www.freepik.com/free-photo/finger-pressing-delete-key_944367.htm#fromView=search&page=1&position=0&uuid=df9eb214-de1b-4daf-83e9-4af345b38a52">Freepik</a>

## Overview

This design not only give you the health of the site, but also a little bit of sense of the architecture
This design area focuses on the process to define a robust health model, mapping quantified application health states through observability and operational constructs to achieve operational maturity.

## Installing Application Insights

### Requirements

1. AzCompute PowerShell Module installed:

```powershell
Install-Module -Name Az.Compute -AllowClobber -Force
```

2. NSG: Allow outbound traffic to service tag `AzureMonitor` on HTTPS (port 443)

2. Networking Requirements: The VM must be able to send outbound HTTPS traffic to Application Insights ingestion endpoints:
```
dc.services.visualstudio.com
*.applicationinsights.azure.com
```

### Connection Application Insights to the intended VM

List of available extension name

```
 az vm extension image list-names -o table --location westeurope -p Microsoft.Azure.Monitor
```

Install Application Insights Agent via Azure CLI

```
az vm extension set \
  --publisher Microsoft.Azure.Monitor \
  --name AzureMonitorWindowsAgent \
  --vm-name EUAZ1-CMC-39 \
  --resource-group COMMVAULT-RG \
  --settings "{ \"connectionString\": \"InstrumentationKey=c9f9404b-a9ea-4a53-9243-f73dec84f344;IngestionEndpoint=https://westeurope-5.in.applicationinsights.azure.com/;LiveEndpoint=https://westeurope.livediagnostics.monitor.azure.com/;ApplicationId=06228d1a-bc60-48f1-ab24-6e1530e88203\" }"
```

Verify Installation

```
az vm extension list --vm-name <VM_NAME> --resource-group <RESOURCE_GROUP> --output table
```

### Test connectivity from source VM to Application Insights

Force use of TLS1.2 version, the 

```powerhsell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
Invoke-WebRequest -Uri "https://dc.services.visualstudio.com" -UseBasicParsing
```

It should return 404 error page, which endpoint is reachable




Let's consider following cases:

1. [Check Agent Status](#1check-agent-status)
2. [Single Agent Removal](#2removing-from-single-vm)
3. [Resource Group Removal](#3resource-group-removal)

## 1.Check Agent Status

![Checking Agents]({{site.baseurl}}/assets/img/2024/60.1.png)

{% include advertising/gl-adsense.html %}
