---
layout: post
title: "Disk encryption Key rotation and File-level restore on Azure VMs"
description: "Options and limitations of Disk Encryption Sets and Azure Disk Encryption when using key-rotation"
categories: [ security, disks, encryption ]
image: assets/img/2024/59.1.jpg
featured: false
author: eloy
comments: true
---

Header image by <a href="https://www.freepik.com/free-photo/html-css-collage-concept-with-person_36295465.htm#query=encryption&position=7&from_view=keyword&track=sph&uuid=a4992635-350c-4f04-ab1d-9e86bd081767">Freepik</a>

- [Overview](#overview)
- [1.Azure Disk Encryption (ADE)](#1azure-disk-encryption-ade)
  - [Customer-managed keys and ADE](#customer-managed-keys-and-ade)
  - [Limitations of Azure Disk Encryption (ADE)](#limitations-of-azure-disk-encryption-ade)
- [2.Azure Disk Encryption Sets (DES)](#2azure-disk-encryption-sets-des)
  - [Customer-managed keys and DES](#customer-managed-keys-and-des)
  - [Advantages of DES](#advantages-of-des)
  - [Disadvantages of Disk Encryption Sets (DES)](#disadvantages-of-disk-encryption-sets-des)
- [3.Encryption at host](#3encryption-at-host)
  - [Advantages of Encryption at host](#advantages-of-encryption-at-host)
  - [Disadvantages of Encryption at host](#disadvantages-of-encryption-at-host)
- [4.Confidential Disk Encryption](#4confidential-disk-encryption)
  - [Advantages of Confidential disk encryption](#advantages-of-confidential-disk-encryption)
  - [Disadvantages of Confidential disk encryption](#disadvantages-of-confidential-disk-encryption)
- [Conclusions](#conclusions)

{% include advertising/gl-adsense.html %}

## Overview

One crucial pillar of [Azure Well-Architected Framework (WAF)](https://learn.microsoft.com/en-us/azure/well-architected/security/checklist) is **Security**, which, among other recommendations, advocates for the following two principles:

1. **Encrypt data *at-rest*** ([SE:07](https://learn.microsoft.com/en-us/azure/well-architected/security/encryption))
2. **Rotating Keys** ([SE:09](https://learn.microsoft.com/en-us/azure/well-architected/security/application-secrets))

Adhering to these two principles, particularly in achieving the <ins>encryption data *at-rest* on disks belonging to Azure VMs</ins>, present us four different options, each with its own advantages and disadvantages, and I would like to delve into based on personal experience.

To make things a bit more challenging, in the realm of Backup/Restore activities in the Azure Cloud, while most software products supports complete restores of entire Azure VMs, the same cannot be said for **file-level restore**. This limitation leads to the need of a full restoration process of the entire disk to access a single file, turning the task into an expensive and time-consuming endeavor. Since the chosen method for encrypting disks is closely tied to the afroementioned statement, it becomes imperative to consider this aspect when selecting the appropiate method for encrypting and rotating keys:

1. **Azure Disk Encryption (ADE)**
2. **Azure Disk Encryption Sets (DES)**
3. **Encryption at host**
4. **Confidential Disk Encryption**

## 1.Azure Disk Encryption (ADE)

This is basically encrypting the OS and data disks of Azure virtual machines (VMs) inside the VM by using the [DM-Crypt](https://wikipedia.org/wiki/Dm-crypt) (Linux) or the classical [BitLocker](https://wikipedia.org/wiki/BitLocker) (Windows), by default with one and only layer of encryption using **platform-managed keys**.

### Customer-managed keys and ADE

It is possible to use **customer-managed keys** on ADE: [envelope encryption](https://learn.microsoft.com/en-us/azure/security/fundamentals/encryption-atrest#envelope-encryption-with-a-key-hierarchy), where a **Key Encryption Key (KEK)** (**customer-managed key**) encrypts a **Data Encryption Key (DEK)**, so we can store keys separated from those used to encrypt data, helping to ensure that the compromise of one entity doesn't affect the other.

### Limitations of Azure Disk Encryption (ADE)

* Use of VM's CPU for encryption/decryption
* Does not work for custom Linux images
* **Key auto-rotation limitation** > To say it simple, <ins>**Azure Disk Encryption (ADE)** is not compatible with key auto-rotation</ins>.

The reason for this is that **ADE** will continue to use the original encryption key even after it has been auto-rotated. [https://learn.microsoft.com/en-us/azure/virtual-machines/windows/disk-encryption-key-vault?tabs=azure-portal#azure-disk-encryption-and-auto-rotations](https://learn.microsoft.com/en-us/azure/virtual-machines/windows/disk-encryption-key-vault?tabs=azure-portal#azure-disk-encryption-and-auto-rotations)

>*This leads to a dead end, as accessing VMs becomes impossible when original keys are disabled or removed due to expiration during a key-rotation.*

To make matters worse, <ins>it is not possible to migrate disks that have ever been encrypted with **Azure Disk Encryption** to another encryption method</ins>

![ADE encrypted disk migration attempt to customer-keys]({{site.baseurl}}/assets/img/2023/59.2.png)

{% include advertising/gl-adsense.html %}

* **File-level restore limitation** > While it is feasible to restore entire VMs, as mentioned earlier, <ins>**ADE** encrypted VMs cannot be recovered at the file/folder level when utilizing [Azure Backup](https://learn.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption#limitations) or [Veeam Backup for Azure](https://helpcenter.veeam.com/archive/vbazure/5a/guide/limitations.html#azure-disk-encryption-)</ins>

## 2.Azure Disk Encryption Sets (DES)

Most Azure **managed-disks** are encrypted with **Azure Storage Encryption**, which uses **Server-Side Encryption (SSE)**, and rely on **platform-managed keys** by default for the encryption.

[Microsoft recommends using server-side encryption to protect your data for most scenarios](https://learn.microsoft.com/en-us/azure/storage/common/storage-service-encryption)

### Customer-managed keys and DES

If you choose to configure your **managed-disks** with **Disk Encryption Set (DES)**, <ins>it will support **customer-managed keys**</ins>. This will provides you with the flexibility to change keys at your own, as this key is utilized to safeguard and control access to the key that encrypts your data.

### Advantages of DES

* **Support for Key rotation on DES** > It also leverage **Envelope encryption**, generating a pair of **data-key** and **customer-managed key**, allowing you to rotate your keys without impacting the VMs. During key rotation, the Storage service re-encrypts the **data encryption keys** only with the new **customer-managed keys**.

I personally tested this scenario using various test VMs, and the outcomes after rotating the customer-managed key are the following:

- If the VM is up and running, will continue working the same, without interruptions
- If the VM is powered off after the rotation happened, a waiting period of one hour is necessary, as the disk requires re-encryption.
- Data key, which remains consistent throughout the process, remains inaccessible nor visible at any level
- Master key or customer-managed key, employed for encrypting the data key, is securely stored in the Key Vault allowing for both **manual or auto-rotation**.

* **File-level restore on DES** > I tested this feature on VMs with disks associated to DES, both on **Veeam Backup for Azure** and **Azure Backup**:

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
* Possibility of **double encryption at rest** > This involves an additional layer of encryption utilizing a different encryption algorithm/mode at the infrastructure layer, employing **platform-managed encryption keys**. This is applicable only to persist OS and data disks, as well as snapshots and images.

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
