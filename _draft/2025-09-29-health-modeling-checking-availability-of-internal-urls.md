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

## Mission-Critical workloads

In the context of [*Well-Architected Framework from Microsoft*](https://learn.microsoft.com/en-us/azure/well-architected/):

* **Workload** > Application resources to support common business goal or execute a common business process, including multiple services, working together to deliver specific end-to-end fucntionality.
* **Mission-Critical** > Criticality scale that covers significat financial cost (business-critical), reputacion or human cost (safety-critical) associated with unavailability or underperformance.

One of the key pilars to achieve such a levels of performance and availability is what is called **Health modeling and observability**.

## Layered application health

Ref: [https://learn.microsoft.com/en-gb/azure/well-architected/mission-critical/mission-critical-health-modeling](https://learn.microsoft.com/en-gb/azure/well-architected/mission-critical/mission-critical-health-modeling)

To build a health model, it is required a deep understanding and study of the intended Mission-Critical workload, with the following high-level steps:

1. **Dependencies** > Define all user flows and map dependencies between functional and logical components, thereby mapping dependencies between Azure resources.
2. **Availability** > Define application health in the context of key business requirements by quantifying `healthy` and `unhealthy` (at all levels, important to distinguish between transient and non-transient failure states)
3. **Usability** > For each application component, refine the definition in the context of what each component does when the application is running normally, but describe them in the context of real user journeys through the system (not as standalone pieces).
4. **Performance** > On top of the system design, add the business expectations for speed and reliability — make sure your component definitions also reflect how fast they must respond and how available they need to be.
5. **Inform** > Finally, aggregate the health states for each individual user flow to form an acceptable representation of the overall application health, thus, should be used to inform critical monitoring metrics across all system components and validate operational subsystem composition.

* Performance testing > To define and continually evaluate application health.

> [!IMPORTANT]
> Failures within a cloud solution may not happen in isolation. An outage in a single component may lead to several capabilities or additional components becoming unavailable.









Let's consider following cases:

1. [Check Agent Status](#1check-agent-status)
2. [Single Agent Removal](#2removing-from-single-vm)
3. [Resource Group Removal](#3resource-group-removal)

## 1.Check Agent Status

![Checking Agents]({{site.baseurl}}/assets/img/2024/60.1.png)

{% include advertising/gl-adsense.html %}
