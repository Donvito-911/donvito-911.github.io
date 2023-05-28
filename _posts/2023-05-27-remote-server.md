---
title: Configuration of Linux remote server
date: 2023-05-27 13:47:10 - 5000
categories: [remote-server]
tags: [ubuntu, linux, server]     # TAG names should always be lowercase
---
## Context
I have 2 computers. One of them is my PC (linux machine) and the other one is my laptop (an old macbook pro). I usually do the hardwork with my PC and use the laptop when I'm outside. The main problem is when I want to run something heavy in my laptop (e.g. training a deep learning network), I'd love to send the tasks to my PC from my laptop and let it do the hard job while I'm not at home. Technically, I want to set a server/client architecture, where my server is my linux machine and the client is my laptop.

<b>Equipment</b>
- PC Linux Ubuntu LTS 22.03 (Server)
- Macbook pro 2012 OS HighSierra (Client)

In this post we'll be exploring the setup of this architecture using SSH. In the first section we'll be allowed to connect from the client to the server when both machines are connected to the same network. In the second section, we'll upgrade the architecture to a completely remote setup (e.g. being in a cafe, university)

## 1. Connect to server
Here we'll be setting the initial configuration so the client can connect to the server when they are in the same network.

### 1.1 Configuration in Linux machine (server)
Update your linux machine and install openssh-server: 
```bash
sudo apt update
sudo apt install openssh-server
```

Check if its running with the command 
```bash
sudo systemctl status ssh
```

![status image](/assets/images/remote-server/status.png "status image")
See that it is active and running.

> If you want to stop/start/restart SSH use ```sudo systemctl <command> ssh``` 
and replace command with one of the options mentioned.
{: .prompt-tip }

Now, you are able to connect via SSH. You'll just need from the linux machine:
1. user and password (the same ones when you log in) 
2. IP address. 

> Get the IP address with the command 
```bash
sudo ip a
```
![status image](/assets/images/remote-server/ipaddress.png "status image")
The blue highlighted is the IP address
{: .prompt-tip }


### 1.2 Configuration in laptop (client)
> This configuration is done using a unix based system (macbook pro), if you are in Windows, you should check out other ways to enable an SSH client such as PUTTY.
{: .prompt-warning }

Open the terminal and run the following command with the user and IP address found in previous step.
```bash
ssh <user>@<IP address of server>
```
It will ask for your password and then you'll be connected to your linux server:

IMAGE

## 2. Remote configuration
First, be sure that you are able to connect to the linux machine following the first section.

Now, in order to connect from any place outside from your network (e.g. from a cafe, university, etc.) you must do some extra steps. These steps involve working mostly with the router, making static IPs, having a domain name for your home network, and others.

### 2.1 Port forwarding
The first step is port forwarding. You must allow your router to receive SSH connections and redirect them to your linux machine. This can be done ...
