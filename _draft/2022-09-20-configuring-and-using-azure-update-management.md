---
title: "Configuring and using Azure Update Management"
excerpt: "How to set Remote Desktop listener certificate to proper internal CA compatible certificate on a Windows Server, domain joined"
date: 2022-09-20
categories:
  - security
tags:
  - Security
  - Updates
  - Patching
header:
  teaser: /assets/img/2021/50.4.png
toc: true # table of contents
toc_sticky: true
---

Updates and Patching are always a headache for any IT-Infra people, so when I saw Azure Update Management wanted to see how far can this go.
Overall experience was not bad, but lot of game rules to keep in mind before start rolling on, so this is what I did as summarised as I can:

## 1.Prerequisites

1. Automation Account connected to a Workspace on Log Analytics
2. Enable 


Supported OS images > [https://learn.microsoft.com/en-us/azure/virtual-machines/automatic-vm-guest-patching](https://learn.microsoft.com/en-us/azure/virtual-machines/automatic-vm-guest-patching)

Enable the Preview for the subscription
```
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

Check for Status (Will take 15+ minutes)

```
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

[https://github.com/MicrosoftDocs/azure-docs/issues/62655#issuecomment-694362024](https://github.com/MicrosoftDocs/azure-docs/issues/62655#issuecomment-694362024)

If you have manually disabled automatic patching in the past it cannot be set to true as of this time. This feature is in preview and this may change in the future. If the enableAutomaticUpdates has not been set (null or undefined) you can set it to true using the following command:

```
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform

az vm update --resource-group B99-DOMAIN-SERVICES --name euaz1-test-90 --set osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform
```