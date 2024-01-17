---
layout: post
title: "Linux equivalent of WSUS with Ansible and Squid"
description: "How to patch linux servers not connected to internet almost automatically, similarly as WSUS in Windows environment"
categories: [ security, linux, ansible, squid, wsus, patching ]
image: assets/img/2022/headers/52.13.jpg
featured: false
author: eloy
comments: true
---

As soon as I saw Linux happily growing up over the landscape, I realized manually patching would not be the best option, so I had to make up something to drive this on a monthly basis.

To make things more interesting, almost all Linux I had were not internet connected, so I ended up tweaking one Linux to serve as a internet proxy (I told you how I did [here](https://eloysalamanca.es/squid/patching-linux-servers-not-connected-to-internet/){:target="_blank"}) and then, used Ansible to spread all patches down.

Let's do it!

{% include advertising/gl-adsense.html %}

## Few words about Ansible

Ansible is purely Infrastructure as Code (IaC), software provisioning, "open source community project sponsored by Red Hat", as they stated on their home page [https://www.ansible.com](https://www.ansible.com){:target="_blank"}

### Architecture

* **Controller** > Computer which orchestrates the solution
* **Nodes** > Every computer handled by controller via SSH

### Pre-Requisites per node

* **SSH** > [Enabled SSH User with root permission without password from Controller](#enabled-ssh-user-with-root-permission-without-password-from-controller)  
* **Python** > Required on every remote server  

## Preparing the Controller

### 1.Installing Ansible

First, added ansible repository and installed as usual

```bash
sudo apt-add-repository ppa:ansible/ansible
sudo apt update
sudo apt install ansible
sudo apt install sshpass -y
```

{% include advertising/gl-adsense.html %}

### 2.Inventory File

After that, made *Inventory file*; containing information about `hosts` to be updated.  
They can be grouped. Also, `variables` can be used to force settings on certain playbooks and templates.

Created this way, with the following content

```bash
sudo nano /etc/ansible/hosts
```

```bash
[linux]
test1 ansible_host=computer1.domain.com
test2 ansible_host=computer2.domain.com

[all:vars]
ansible_user='ansible'
ansible_become=yes
ansible_become_method=sudo
ansible_python_interpreter='/usr/bin/python3'
```

Checked inventory file this way:

```bash
ansible-inventory --list -y
```

![Testing Inventory file]({{site.baseurl}}/assets/img/2022/52.9.png)

{% include advertising/gl-adsense.html %}

### 3.SSH Key

Generated an SSH key on the Controller

```bash
ssh-keygen

[Enter]  [Enter]  [Enter]
```

![Generated SSH key]({{site.baseurl}}/assets/img/2022/52.10.png)

## Preparing Nodes (Clients)

### Enabled SSH User with root permission without password from Controller

1.Added `ansible` user on every destination host or Node

```bash
sudo adduser ansible
```

![Adding ansible User]({{site.baseurl}}/assets/img/2022/52.3.png)

2.Configured password-less sudo access on every Node

```bash
echo "ansible ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible
```

![Configured password-less]({{site.baseurl}}/assets/img/2022/52.4.png)

3.From the Controller, copied SSH public key to every Node

```bash
ssh-copy-id ansible@[COMPUTER_NAME]
```

![Copied SSH public key]({{site.baseurl}}/assets/img/2022/52.5.png)

4.On every Node, disabled password-based login for the `ansible` user

```bash
sudo usermod -L ansible
```

![Disabled password-based loging for user]({{site.baseurl}}/assets/img/2022/52.6.png)

5.From the `Controller`, checked everything was properly set

```bash
ansible all -m ping -u ansible
```

![Testing hosts file]({{site.baseurl}}/assets/img/2022/52.11.png)

{% include advertising/gl-adsense.html %}

## Listing Pending Updates

Prepared file `/etc/ansible/list-updates.yml` with the following content

```yaml
---
- name: Updates Linux Ubuntu 20.4 Servers
  hosts: linux
  become: true
  become_user: root
  vars:
    proxy_env:
      http_proxy: http://[COMPUTER_NAME_PROXY]:8080
  tasks:
  # collect information
  - name: apt-get listonly collect
    shell: apt-get --just-print upgrade 2>&1 | perl -ne 'if (/Inst\s([\w,\-,\d,\.,~,:,\+]+)\s\[([\w,\-,\d,\.,~,:,\+]+)\]\s\(([\w,\-,\d,\.,~,:,\+]+)\)? /i) {print "PROGRAM= $1 INSTALLED= $2 AVAILABLE= $3\n"}' | column -s ' ' -t
    args:
      warn: false
    register: apt_listonly
    changed_when: false
    environment: "{{ proxy_env }}"

  # apt-get listonly output
  - name: needrestart -lb output
    debug: var=apt_listonly.stdout_lines
    changed_when: false
    environment: "{{ proxy_env }}"
```

![List Updates file]({{site.baseurl}}/assets/img/2022/52.12.png)

Listed pending updates:

```bash
ansible-playbook -i hosts list-updates.yml -v
```

![Pending updates list]({{site.baseurl}}/assets/img/2022/52.13.png)

{% include advertising/gl-adsense.html %}

## Applying Pending Updates

Prepared file `/etc/ansible/apply-updates.yml` with the following content

```yaml
---
- hosts: linux  become: true
  become_user: root
  vars:
    proxy_env:
      http_proxy: http://[COMPUTER_NAME_PROXY]:8080
  tasks:
    - name: Update apt repo and cache on all Debian/Ubuntu boxes
      apt: update_cache=yes force_apt_get=yes cache_valid_time=3600
      environment: "{{ proxy_env }}"

    - name: Upgrade all packages on servers
      apt: upgrade=dist force_apt_get=yes
      environment: "{{ proxy_env }}"

    - name: Check if a reboot is needed on all servers
      register: reboot_required_file
      stat: path=/var/run/reboot-required get_md5=no

    - name: Reboot the box if kernel updated
      reboot:
        msg: "Reboot initiated by Ansible for kernel updates"
        connect_timeout: 5
        reboot_timeout: 300
        pre_reboot_delay: 0
        post_reboot_delay: 30
        test_command: uptime
      when: reboot_required_file.stat.exists
```

Applied pending updates:

```bash
ansible-playbook -i hosts apply-updates.yml -v
```

![Applied updates]({{site.baseurl}}/assets/img/2022/52.14.png)

In this case, both nodes were updated without errors (when they appears, normally are in red)

{% include advertising/gl-adsense.html %}