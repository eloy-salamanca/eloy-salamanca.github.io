---
layout: post
title: "Key rotation on Disk Encryption Sets and Azure Disk Encryption"
description: "Options and limitations of Disk Encryption Sets and Azure Disk Encryption when using key-rotation"
categories: [ security, disks, encryption ]
image: assets/img/2023/59.1.png
featured: false
author: eloy
comments: true
---

One important pillar of [Azure Well-Architected Framework (WAF)](https://learn.microsoft.com/en-us/azure/well-architected/security/checklist) is **Security**, which, among other recommendations, suggests the following:

1. Encrypt data *at-rest*, *in-transit* and *in-use*:

> [SE:07](https://learn.microsoft.com/en-us/azure/well-architected/security/encryption) **Encrypt data** by using modern, industry-standard methods to guard confidentiality and integrity. Align the encryption scope with data classifications, and prioritize native platform encryption methods.

2. Rotating Keys

> [SE:09](https://learn.microsoft.com/en-us/azure/well-architected/security/application-secrets) Protect application secrets by hardening their storage and restricting access and manipulation and by auditing those actions. **Run a reliable and regular rotation process that can improvise rotations for emergencies**.

## Azure Disk Encryption (ADE)

One of the available solutions we could think of for both options could be **Azure Disk Encryption (ADE)**, which is encrypting the OS and data disks of Azure virtual machines (VMs) inside your VMs by using the [DM-Crypt](https://wikipedia.org/wiki/Dm-crypt) (Linux) or the [BitLocker](https://wikipedia.org/wiki/BitLocker) (Windows).

Unfortunatelly, it have 2 main drawbacks:

### Key auto-rotation limitation

To say it simple, <ins>ADE is incompatible with key auto-rotation</ins>.

The reason is that **Azure Disk Encryption** will continue using the original encryption key, even after it has been auto-rotated, and [this is advised on the Microsoft learning site](https://learn.microsoft.com/en-us/azure/virtual-machines/windows/disk-encryption-key-vault?tabs=azure-portal#azure-disk-encryption-and-auto-rotations)

This will drive to dead end, due to the impossibility to access VMs when original keys are disabled or removed when expired.

### File-level restore limitation

Although it is possible to restore entire VMs, ADE encrypted VMs can't be recovered at the file/folder level when using [Azure Backup](https://learn.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption#limitations) or [Veeam Backup for Azure](https://helpcenter.veeam.com/archive/vbazure/5a/guide/limitations.html#azure-disk-encryption-)

## Azure Disk Encryption Sets (DES)

Customer-keys give you the flexibility to change keys at your own, and this compelling reason to use instead of Platform-managed keys, where Azure is responsible for key management.

It uses [Envelope encryption](https://learn.microsoft.com/en-us/azure/security/fundamentals/encryption-atrest#envelope-encryption-with-a-key-hierarchy), where a **Key Encryption Key (KEK)** encrypts a **Data Encryption Key (DEK)**. This way, we can store keys separated from encrypted data, helping to ensure that the compromise of one entity doesn't affect the other. 


 allowing you to rotate the keys periodically without impacting the VMs. When rotating the keys, Storage service re-encrypts the **data encryption** keys only with the new **customer-managed keys**

* Indicar que cuando se ha encriptado disco con ADE, ya no se puede usar customer-managed disk

* Indicar lo que pasa cuando se rota una key con Disk Encryption Sets

{% include advertising/gl-adsense.html %}

![Adding ansible User]({{site.baseurl}}/assets/img/2023/59.1.png)

```bash
ansible all -m ping -u ansible
```

{% include advertising/gl-adsense.html %}
