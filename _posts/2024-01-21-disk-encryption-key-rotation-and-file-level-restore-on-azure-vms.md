---
layout: post
title: "Disk encryption Key rotation and File-level restore on Azure VMs"
description: "Choices and constraints in encrypting Azure VM disks and their impact on file-level restores"
categories: [ security, disks, encryption ]
image: assets/img/2024/59.1.jpg
featured: true
author: eloy
comments: true
---

Choices and constraints in encrypting Azure VM disks and their impact on file-level restores

Header image by <a href="https://www.freepik.com/free-photo/html-css-collage-concept-with-person_36295465.htm#query=encryption&position=7&from_view=keyword&track=sph&uuid=a4992635-350c-4f04-ab1d-9e86bd081767">Freepik</a>

## Overview

One crucial pillar of [Azure Well-Architected Framework (WAF)](https://learn.microsoft.com/en-us/azure/well-architected/) is **Security**. This involves a couple of important suggestions, like:

1. **Encrypt data *at-rest*** ([SE:07](https://learn.microsoft.com/en-us/azure/well-architected/security/encryption))
2. **Rotating Keys** ([SE:09](https://learn.microsoft.com/en-us/azure/well-architected/security/application-secrets))

Following these principles, especially when it comes to <ins>encryption data *at-rest* on disks belonging to Azure VMs</ins>, offers us four different options, each with its own pros and cons, and I would like to dive into these options based on personal experience.

To make things a bit more challenging, in the realm of Backup/Restore operations in the Azure Cloud, while most software products support full restores of entire Azure VMs, the same isn't true for **file-level restore**. This limitation means having to through a complete restoration process of the entire disk just to get one file, turning the task into something costly and time-intensive. Given the close connection between the chosen disk encryption method and the mentioned scenario, it's crucial to factor this in when deciding the right approach for encrypting and rotating keys:

1. [**Azure Disk Encryption (ADE)**](#1azure-disk-encryption-ade)
2. [**Azure Disk Encryption Sets (DES)**](#2azure-disk-encryption-sets-des)
3. [**Encryption at host**](#3encryption-at-host)
4. [**Confidential Disk Encryption**](#4confidential-disk-encryption)

## 1.Azure Disk Encryption (ADE)

This essentially involves encrypting both the OS and data disks of Azure virtual machines (VMs) within the VM itself. This is achieved using [DM-Crypt](https://wikipedia.org/wiki/Dm-crypt) (Linux) or the traditional [BitLocker](https://wikipedia.org/wiki/BitLocker) (Windows). The default configuration employs a single layer of encryption using **platform-managed keys**.

### Customer-managed keys and ADE

Using **customer-managed keys** with ADE is feasible through [envelope encryption](https://learn.microsoft.com/en-us/azure/security/fundamentals/encryption-atrest#envelope-encryption-with-a-key-hierarchy), where a **Key Encryption Key (KEK)** (**customer-managed key**) encrypts a **Data Encryption Key (DEK)**. This allows us to store keys separately from those used for data encryption, ensuring that the compromise of one entity doesn't impact the other.

### Limitations of Azure Disk Encryption (ADE)

* Utilization of VM's CPU for encryption/decryption
* Incompatibility with custom Linux images
* **Key auto-rotation limitation** > To say it simple, <ins>**Azure Disk Encryption (ADE)** doesn't support key auto-rotation</ins>.

This is because **ADE** persists in using the original encryption key even after it undergoes auto-rotation [https://learn.microsoft.com/en-us/azure/virtual-machines/windows/disk-encryption-key-vault?tabs=azure-portal#azure-disk-encryption-and-auto-rotations](https://learn.microsoft.com/en-us/azure/virtual-machines/windows/disk-encryption-key-vault?tabs=azure-portal#azure-disk-encryption-and-auto-rotations)

>*This situation leads to a dead end, rendering VMs access impossible when the original keys are disabled or removed due to expiration during a key-rotation.*

To make matters worse, <ins>it is not feasible to migrate disks that have ever been encrypted with **Azure Disk Encryption** to another encryption method</ins>

![ADE encrypted disk migration attempt to customer-keys]({{site.baseurl}}/assets/img/2023/59.2.png)

{% include advertising/gl-adsense.html %}

* **File-level restore limitation** > While restoring entire VMs is an option, as mentioned earlier, <ins>**ADE** encrypted VMs cannot be recovered at the file/folder level when utilizing [Azure Backup](https://learn.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption#limitations) or [Veeam Backup for Azure](https://helpcenter.veeam.com/archive/vbazure/5a/guide/limitations.html#azure-disk-encryption-)</ins>

## 2.Azure Disk Encryption Sets (DES)

The majority of Azure **managed-disks** are encrypted with **Azure Storage Encryption**, employing **Server-Side Encryption (SSE)**, and relying on **platform-managed keys** by default for encryption.

[Microsoft recommends using server-side encryption to protect your data for most scenarios](https://learn.microsoft.com/en-us/azure/storage/common/storage-service-encryption)

### Customer-managed keys and DES

If you opt to configure your **managed-disks** with **Disk Encryption Set (DES)**, <ins>it will support **customer-managed keys**</ins>. This provides you with the flexibility to change keys as needed, as this key is used to secure and control access to the key encrypting your data.

### Advantages of DES

* **Support for Key rotation on DES** > It also leverage **Envelope encryption**, creating a pair of **data-key** and **customer-managed key**, enabling you to rotate your keys without affecting the VMs. During key rotation, the Storage service re-encrypts the **data encryption keys** only with the new **customer-managed keys**.

I personally tested this scenario using various test VMs, and the outcomes after rotating the customer-managed key are as follows:

- If the VM is up and running, it continues working the same without interruptions.
- If the VM is powered off after the rotation, a waiting period of one hour is necessary, as the disk requires re-encryption.
- Data key, which remains consistent throughout the process, remains inaccessible and invisible at any level
- Master key or customer-managed key, used for encrypting the data key, is securely stored in the Key Vault, allowing for both **manual or auto-rotation**.

* **File-level restore on DES** > I tested this feature on VMs with disks associated with DES, both using **Veeam Backup for Azure** and **Azure Backup**:

![Veeam Backup for Azure file-level restore]({{site.baseurl}}/assets/img/2023/59.3.png)

![Azure Backups file-level restore]({{site.baseurl}}/assets/img/2023/59.4.png)

{% include advertising/gl-adsense.html %}

### Disadvantages of Disk Encryption Sets (DES)

* Neither **Azure Disk Storage Encryption** nor when configured with **Disk Encryption Set (DES)** <ins>will encrypt temp disks or disk caches</ins>
* <ins>The status of **Microsoft Defender for Cloud** will always be reported as **UNHEALTHY!!**, since it solely recognizes the presence of **Azure Disk Encryption**</ins> [https://learn.microsoft.com/en-us/azure/virtual-machines/disk-encryption-overview#comparison](https://learn.microsoft.com/en-us/azure/virtual-machines/disk-encryption-overview#comparison)

## 3.Encryption at host

For disks with encryption at host enabled, <ins>the server hosting the VM is responsible for encrypting your data</ins>.

### Advantages of Encryption at host

* It enhances **Azure Disk Storage Server-Side Encryption**, ensuring that all **temp disks** and **disk caches** are also encrypted at rest. 
* Possibility of **double encryption at rest** > This involves an additional layer of encryption using a different encryption algorithm/mode at the infrastructure layer, employing **platform-managed encryption keys**. This is applicable only to persist OS and data disks, as well as snapshots and images.

### Disadvantages of Encryption at host

* 4k sector size **Ultra Disks** and **Premium SSD v2** disks are not supported
* It is incompatibility with **Azure Disk Encryption**, meaning that already encrypted VMs usin this method won't support the **Encryption at host** feature.
* After enabling **Encryption at host**, if there are existing VMs, they must be deallocated and reallocated to be encrypted

## 4.Confidential Disk Encryption

Associates the disk encryption keys to the VM's TPM, making them accessible only to that specific VM.

### Advantages of Confidential disk encryption

* Introducing a new and improved disk encryption scheme that safeguards all critical partitions on the disk.
* To mitigate potential security threats, a dedicated and separate cloud service encrypts the disk during the VM's initial creation.

### Disadvantages of Confidential disk encryption

* Currently, this feature is exclusively available for the OS disk. To overcome this limitation, consider utilizing **Encryption at host** for disks other than OS disks.
* Starting from July 20222, encrypted OS disks will icur higher costs.

## Conclusions

1. **Disk Encryption Sets (ADE)** offer several advantages over **Azure Disk Encryption (ADE)**, including native support for file-level restores and comprehensive support for encryption key auto-rotation. Beyond being recommended by Microsoft, <ins>it appears to be the optimal choice for encrypting disks and aligning with key Security principles of the Well-Architect Framework</ins>. However, it is essential for Enterprise Security to carefully assess two significatn drawbacks: the inability of encrypting temp and cache disks, and the persistent unhealthy status reported by Microsoft Defender for Cloud.

4. **Encryption at host** is a closely related alternative to **Disk Encryption Set (ADE)**, focusing on the encryption of temp and cache disks. However, it has an unhealthy status on the Microsoft Defender Cloud, and comes with the limitation that Ultra Disks or Premium SSD v2 disks are not covered.

{% include advertising/gl-adsense.html %}
