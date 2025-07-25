---
title: "Health modeling on Mission-Critical workloads"
description: "Health modeling and Observability of Mission-Critical workloads"
categories: [ monitoring ]
image: assets/img/2024/62.0.jpg
featured: true
author: eloy
comments: true
---

Options to remove Azure Log Monitoring Agent (MMA or OMS) individually and in bulk using Azure CLI

Header image by <a href="https://www.freepik.com/free-photo/finger-pressing-delete-key_944367.htm#fromView=search&page=1&position=0&uuid=df9eb214-de1b-4daf-83e9-4af345b38a52">Freepik</a>

+info[Health modeling and observability of mission-critical workloads on Azure](https://learn.microsoft.com/en-gb/azure/well-architected/mission-critical/mission-critical-health-modeling)

## Overview

This design not only give you the health of the site, but also a little bit of sense of the architecture

This design area focuses on the process to define a robust health model, mapping quantified application health states through observability and operational constructs to achieve operational maturity.

## Layered application health

1. Define all user flows and map dependencies between functional and logical components.
2. Define application health in the context of key business requirements by quantifying `healthy` and `unhealthy` (at all levels, important to distinguish between transient and non-transient failure states)
3. **Layered health model** > For each application component, refine the definition in the context of a steady running state and aggregated according to the application user flows.
4. Superimpose with key non-functional business requirements for performance and availability.
5. Performance testing > To define and continually evaluate application health.
6. Finally, aggregate the health states for each individual user flow to form an acceptable representation of the overall application health.

> [!IMPORTANT]
> Failures within a cloud solution may not happen in isolation. An outage in a single component may lead to several capabilities or additional components becoming unavailable.









Let's consider following cases:

1. [Check Agent Status](#1check-agent-status)
2. [Single Agent Removal](#2removing-from-single-vm)
3. [Resource Group Removal](#3resource-group-removal)

## 1.Check Agent Status

![Checking Agents]({{site.baseurl}}/assets/img/2024/60.1.png)

{% include advertising/gl-adsense.html %}
