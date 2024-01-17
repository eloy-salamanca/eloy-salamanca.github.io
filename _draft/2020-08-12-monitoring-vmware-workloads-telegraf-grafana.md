---
layout: post
comments: true
title: Monitoring VMware workloads using Telegraf and Grafana
excerpt: Step by step guide to install and configure Linux Server to monitor VMware workloads with InfluxDB, Telefraf and Grafana
date: 2020-08-12 11:12:00 +0300
img: 47.1.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
category: monitoring
tags: [monitoring, influxdb, telegraf, grafana, linux] # add tag
---

As I had to define new monitoring system from scratch, and now testing how InfluxDB, Telegraf and Grafana performs on a on-premises environment.

- **InfluxDB** > InfluxDB is an Open Source Time Series Database Platform for storing Time Series Data or what we are looking for: metrics & Events collected from different devices
- **Telegraf** > Agent written in Go for collecting performance metrics from the system it’s running on and the services running on that system. Collected metrics can be sink into InfluxDB.
- **Grafana** > Grafana is an open source, feature rich metrics dashboard and graph editor for Graphite, Elasticsearch, OpenTSDB, Prometheus, and InfluxDB. As an example, when using InfluxDB as your data source, data stored on InfluxDB will be visualized using Grafana Graphs.

* [Prerequisites](#prerequisites)
* [InfluxDB](#influxdb)
* [Grafana](#grafana)

---

<div id='prerequisites' />

## Prerequisites

- Ubuntu 20.4 Linux Server
- Docker and docker-compose, latest versions

---

<div id='influxdb' />

## InfluxDB
Process: 
- First of all, adding InfluxDB repo to our Ubuntu 20.04 is required
```
echo "deb https://repos.influxdata.com/ubuntu bionic stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
```
- Import GPG key
```
sudo curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
```
- Update and install InfluxDB on Ubuntu 20.04
```
sudo apt-get update
sudo apt-get install influxdb
```
- Start and enable the service to start on boot up
```
sudo systemctl enable --now influxdb
```
- Check service status
```
 systemctl status influxdb
 ```
![InfluxDB up and running]({{site.baseurl}}/assets/img/47.1.png)

### Configure InfluxDB
- Add user/passwd to use InfluxDB
```
sudo nano /etc/influxdb/influxdb.conf
```
- uncomment enabled on http endpoint
```
[http]
# Determines whether HTTP endpoint is enabled.
enabled = true
# Determines whether the Flux query endpoint is enabled.
# flux-enabled = false
```
- Restart InfluxDB
```
sudo systemctl restart influxdb
```
- Create admin account
```
curl -XPOST "http://localhost:8086/query" --data-urlencode "q=CREATE USER superadmin WITH PASSWORD 'type_password_here' WITH ALL PRIVILEGES"

---

<div id='grafana' />

## Grafana
Process:

- Add Grafana gpg key
```
sudo apt-get install -y gnupg2 curl  software-properties-common
curl https://packages.grafana.com/gpg.key | sudo apt-key add -
```
- Install Grafana APT repository
```
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
```
- Update repositories and add Grafana
```
sudo apt-get update
sudo apt-get -y install grafana
```
- Start Grafana
```
sudo systemctl enable --now grafana-server
```
- Check service status
```
systemctl status grafana-server.service
```

![Grafana up and running]({{site.baseurl}}/assets/img/47.2.png)
![Grafana up and running]({{site.baseurl}}/assets/img/47.3.png)

---

## Telegraf
Process
- Install Telegraf
```
sudo apt-get update
sudo apt-get install telegraf
```
- To configure Telegraf to pull Monitoring metrics from vCenter, it's important to tick conf file
```
sudo vim /etc/telegraf/telegraf.conf
```
1. Add InfluxDB output storage backend where metrics will be stored.
```
