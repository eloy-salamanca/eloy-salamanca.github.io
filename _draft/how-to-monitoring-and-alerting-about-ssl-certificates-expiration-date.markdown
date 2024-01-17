---
layout: post
comments: true
title: How to Monitoring and Alerting about SSL Certificates Expiration Date
date: 2018-01-10 10:35:00 +0300
description: How to enable PowerShell remotely to run scripts
img: 17.1.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [monitoring, alerting, certificates, ssl, ssl certificates, docker] # add tag
---

One of the most common and silly sources of incidents can be an SSL certificate expired, I have been fighting against this for a while after seeing how different services came down without an apparent true reason.

As I was looking for something as automatic as possible, and avoid manual operations (like calendar appointments and other error-prone manual extravagances) I recently found this little python toy from [roymartinezblanco on github](https://github.com/roymartinezblanco/akamai-ssl-expiration-audit), which I have used to compose a tiny Web Monitoring and email-based Alert system.

Using such a tool, and with the aim of automate as much as possible, I made use of [Flask](https://palletsprojects.com/p/flask/), a simple python based web framework over [Ubuntu Docker Image](https://hub.docker.com/_/ubuntu); this post will briefly describe how I managed to get it running:

## 1.Install Docker CE on Ubuntu
Followed regular [Docker CE (Community Edition) documentation:](https://docs.docker.com/v17.09/engine/installation/linux/docker-ce/ubuntu/#install-using-the-repository)

### Set up repository
1. Update the `apt` package index:
```bash
sudo apt-get update
```
2. Installed packages to allow apt to use a repository over HTTPS:
```bash
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```
3. Added Docker’s official GPG key:
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
4. Verified I had the right fingerprint
```bash
sudo apt-key fingerprint 0EBFCD88
```
5. Result should be something very similar to this:
```bash
pub   4096R/0EBFCD88 2017-02-22
      Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid                  Docker Release (CE deb) <docker@docker.com>
sub   4096R/F273FCD8 2017-02-22
```
5. Set up **stable** repository:
```bash
sudo apt-add-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

### Install Docker CE
1. Update the `apt` package index:
```bash
sudo apt-get update
```
2. Make sure you are about to install from the Docker repo instead of the default:
```bash
apt-cache policy docker-ce
```
3. Install the latest version of Docker CE and `docker-compose`
```bash
sudo apt-get install docker-ce docker-compose
```
4. Verify that Docker CE is installed correctly by running the `hello-world` image:
```bash
sudo docker run hello-world
```

### Manage Docker as a non-root user
1. Added normal `$USER` to `docker` group
```bash
sudo usermod -aG docker $USER
```
2. Log out and log back in so that your group membership is re-evaluated
3. Verify that is possible run `docker` commands without `sudo`
```bash
docker run hello-world
```

## 2.Create folder structure
I created following folder and put everything there:
```bash
mkdir -p ~/certs-expiry-checker/app
cd ~/certs-expiry-checker/app
```

## 3.Download and save ssl-audit script into certs-expiry-checker/app folder
I won’t go into many details; [Roy Martinez created and explained it very well on his github](https://github.com/roymartinezblanco/akamai-ssl-expiration-audit)

Only I had some difficulties with dependencies, because documentation recommends `pip` and script was written in `python3`. 
After re-installed dependencies using `pip3`, as I’m going to explain in the next section (using **Dockerfile**) everything worked as expected.

As I tweaked out some settings and prepare a few things, it is advisable to download all from following [github repository](https://github.com/eloy-salamanca/certs-expiry-checker.git)

I downloaded and put everything under `~/certs-expiry-checker/app` folder

## 4.Prepare ubuntu 18.04 Docker image
Pull ubuntu 18.04 image:
```bash
docker pull ubuntu:18.04
```

## 5.Build customized image
Let's Build custom image
```bash
cd ~/certs-expiry-checker
docker build -t certs-expiry-checker:0.1 .
```

## 6.Run customized image
Run Docker image:
```bash
docker image ls
docker run -dit -p 80:5000 certs-expiry-checker:0.1 /bin/bash
```
Check for Container-ID and that is “Up”
```bash
docker ps -a
```
Connect to the Docker container
```bash
docker exec -it [CONTAINER_ID] /bin/sh
```
Start Flask App
```bash
flask run
```
Detaching from the container without stopping: `CTRL-P`+`CTRL-Q`

![Getting access to the container]({{site.baseurl}}/assets/img/17.1.png)
