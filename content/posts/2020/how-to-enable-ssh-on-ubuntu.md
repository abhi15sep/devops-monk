---
title: How to Enable SSH on Ubuntu ?
author: Abhay
type: post
date: 2020-07-18T02:05:05+00:00
url: /2020/07/how-to-enable-ssh-on-ubuntu/
blogger_permalink:
  - /2020/07/how-to-enable-ssh-on-ubuntu.html
post_views_count:
  - 5
categories:
  - Linux
tags:
  - SSH
---

Secure Shell (SSH) is a protocol used to securely log onto remote machines. It is the most common way to access remote Linux servers.

<img class="alignnone size-medium" src="/images/virtualbox.jpg" alt="virtualbox"  />
<img class="alignnone" src="/images/prompt.jpg" alt="prompt" />

### 1. Enabling SSH on Ubuntu

The SSH server is not installed by default on Ubuntu desktop systems but it can be easily installed from the standard Ubuntu repositories.

To install and enable SSH on your Ubuntu system complete the following steps:

`sudo apt-get install openssh-server`

Once the installation is completed, the SSH service will start automatically. To verify that the installation was successful and SSH service is running type the following command which will print the SSH server status:

`sudo systemctl status ssh`

Ubuntu comes with a firewall configuration tool called UFW. If the firewall is enabled on your system, make sure to open the SSH port:

`sudo ufw allow ssh`

Now that SSH is installed and running on your Ubuntu system you can connect to it via SSH from any remote machine. Linux and macOS systems have SSH clients installed by default. If you want to connect from a Windows machine then you can use an SSH client such as PuTTY

### 2. Connecting to SSH Over LAN

To connect to your Ubuntu machine over LAN you only need to enter the following command:

`ssh username@ip_address`

If you don’t know your IP address you can easily find it using the ip command :

`ip a`

Once you’ve found the IP address, login to remote machine by running the following ssh command:

ssh user@192.168.121.111

Enjoy 🙂
