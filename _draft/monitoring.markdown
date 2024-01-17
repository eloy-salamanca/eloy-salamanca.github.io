---
layout: post
comments: true
title: Monitoring
excerpt: How to effectively monitor Azure workloads
date: 2019-11-26 08:00:00 +0300
img: 40.1.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
category: monitoring
tags: [Monitoring, Azure] # add tag
---

Every SysAdmin knows about monitoring, but effective monitoring is different thing.




# White-box monitoring
Monitoring based on internal systems metrics. Depends on the ability to inspect the innards of the system: *logs*, *HTTP endpoints*, etc. White-box monitring allows detection of imminent problems.

# Black-box monitoring
What the user see. This is *symptom-oriented* and represents active -not predicted- problems, like: "Users are getting 500s error response"

# Alerts
Notifications for humans to take action: **tickets**, **email alerts**, and **pages**

![Enable Remote PowerShell]({{site.baseurl}}/assets/img/11.1.png)

> In a multilayered system, one person's symptom is another person's cause.

- **Debugging** > white-box monitoring is essential.
- **Paging** > black-box monitoring only nag a human when a problem is both already ongoing and contributing to real symptoms.

# The Four Golden Signals


## Latency
The time it takes to service a request. Important to distinguish successful requests and failed requests.

## Traffic
How much demand is being placed on your system, measured in a high-level system-specific metric. Ex: HTTP request per second.

## Errors
The rate of requests that fail, either explicitly (e.g. HTTP 500s), implicitly (for example, an HTT 200 success response, but coupled with the wrong content), or by policy (for ex: "if you committed to one-second response times, any request over one second is an error").

## Saturation
How "full" your service is. Now that many systems defrade in performance before they achieve 100% utilization, so having a utilization target is essential. Indirect signals: CPU Utilization, network bandwidth that have a known upper bound. Measuring your 99th percentile response time over some smal window (e.g., one minute) can give a very early signal of saturation.
