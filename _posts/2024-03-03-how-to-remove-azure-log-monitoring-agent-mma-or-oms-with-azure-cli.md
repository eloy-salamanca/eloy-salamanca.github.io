---
title: "How to remove Azure Log Monitoring Agent MMA or OMS massively with Azure CLI"
description: "Options to remove Azure Log Monitoring Agent (MMA or OMS) individually and in bulk using Azure CLI"
categories: [ monitoring ]
image: assets/img/2024/60.0.jpg
featured: true
author: eloy
comments: true
---

Options to remove Azure Log Monitoring Agent (MMA or OMS) individually and in bulk using Azure CLI

## Overview

It is well know that the deadline for deprecation of the Azure Microsoft Monitoring Agent (MMA) is the end of August, 2024 [https://azure.microsoft.com/en-gb/updates/were-retiring-the-log-analytics-agent-in-azure-monitor-on-31-august-2024/](https://azure.microsoft.com/en-gb/updates/were-retiring-the-log-analytics-agent-in-azure-monitor-on-31-august-2024/)

It is also advisable to deploy the new Azure Monitor Agent (AMA) before uninstalling old one, double checking first that all functionalities are covered, and then proceed with the removal. However, the question arises: how can this be done remotely with minimum interaction with the guest VM?

Let's consider following cases:

1. [Check Agent Status](#1check-agent-status)
2. [Single Agent Removal](#2removing-from-single-vm)
3. [Resource Group Removal](#3resource-group-removal)

## 1.Check Agent Status

First of all, it is important to check current status, to verify if the intended VM already has both the new and old agent. The best way to do this is by checking the relevant Log Anlytics Workspace, where ultimately, all events end up.

Following Kusto Query can help to achieve it:

```kusto
Heartbeat
| summarize max(TimeGenerated) by Computer, Category, ComputerIP
| where Computer like "COMPUTER_NAME"
| distinct Computer, Category, ComputerIP
| sort by Computer asc
```

Those reported with Category labeled as "Direct Agent" are our target (Log Analytics Agent, MMA or OMS, as you prefer to call it)

![Checking Agents]({{site.baseurl}}/assets/img/2023/60.1.png)

{% include advertising/gl-adsense.html %}

## 2.Removing from single VM

Although it is possible to remove from Azure Portal, it can also be done using Azure CLI. This involves first connecting to the platform and ensuring the correct subscription is selected

```azurecli
az login
az account set -s "SUBSCRIPTION"
az account show
```

After this, the following line will remove all MMA extensions:

```azurecli
az vm extension delete -g RESOURCE_GROUP_NAME --vm-name VM_NAME -n MicrosoftMonitoringAgent --verbose
```

## 3.Resource Group Removal

Traversing all VMs in a Resource Group and deleting all extensions is certainly a very appealing option, as shown below:

```bash
#!/usr/bin/env bash

rg="RESOURCE-GROUP-NAME"

az vm list -g $(echo $rg) --show-details --query "[*].{Name:name}" --output tsv | while read -r vm; do
    echo $vm
    echo 'az vm extension delete -g $(echo $rg) --vm-name $(echo $vm) -n MicrosoftMonitoringAgent --verbose' 
    az vm extension delete -g $(echo $rg) --vm-name $(echo $vm) -n MicrosoftMonitoringAgent --verbose
    if [ $? -ne 0 ]; then
        echo "Failed to delete Extension on $vmList"
        exit 1
    else
        echo "$vmList Extensions deleted"
    fi        
done
```

![Checking Agents]({{site.baseurl}}/assets/img/2023/60.2.png)

From that on, it is also possible to loop through all resource-groups listed in a text file or even traverse all resource-groups within a subscription to automatically remove everything all at once! (ask for this option in the comments in you'd like to see it)

{% include advertising/gl-adsense.html %}
