---
title: "How to see and remove diagnostic settings from Azure VMs"
excerpt: "Options to see and remove diagnostic settings from Azure VMs using Azure CLI"
date: 2023-09-06
categories:
  - compute
tags:
  - azure-cli
  - azure-vm
  - compute
header:
  teaser: /assets/img/2024/61.1.png
  og_image: /assets/img/2024/61.1.png
toc: true # table of contents
toc_sticky: true
last_modified_at: 
---

1. Login into Azure with appropriate role, select the intended subscription

2. List current Diagnostic Settings in the VM:

```
az monitor diagnostic-settings list --resource EUAZ1-EXSTP1-37 --resource-group rg-exstream-infra-prod-we-001 --resource-type Microsoft.Compute/virtualMachines
```

![List of all diagnostic settings from an Azure VM]({{site.baseurl}}/assets/img/2024/61.1.png)

{% include advertising/gl-adsense.html %}

3. Remove undesired diagnostic setting:

```
az monitor diagnostic-settings delete --name mdgs-vm-EUAZ1-EXSTP1-37-prod-we-001 --resource EUAZ1-EXSTP1-37 --resource-group rg-exstream-infra-prod-we-001 --resource-type Microsoft.Compute/virtualMachines
```

![List of all diagnostic settings from an Azure VM]({{site.baseurl}}/assets/img/2024/61.2.png)

Check that was removed correctly

---

{% include advertising/gl-adsense.html %}
