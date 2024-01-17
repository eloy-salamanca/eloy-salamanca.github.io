---
title: "SSH Keys on Azure Linux VMs"
excerpt: "Options and limitations of Disk Encryption Sets and Azure Disk Encryption"
date: 2023-11-23
categories:
  - security
tags:
  - security
  - disks
  - encryption
header:
  teaser: /assets/img/2023/59.1.png
  og_image: /assets/img/2023/59.1.png
toc: true # table of contents
toc_sticky: true
last_modified_at: 
---

* Encrypting disks is an important thing to do, but probably more important is rotating keys

* Indicar que el file-level restore no funciona con Azure Disk Encryption, pero si funciona en Azure Disk Encryption Sets

* Indicar que cuando se ha encriptado disco con ADE, ya no se puede usar customer-managed disk

* Indicar lo que pasa cuando se rota una key con Disk Encryption Sets

{% include advertising/gl-adsense.html %}

![Adding ansible User]({{site.baseurl}}/assets/img/2023/59.1.png)

```bash
ansible all -m ping -u ansible
```

{% include advertising/gl-adsense.html %}
