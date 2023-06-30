---
title: Configuration of Linux remote server
date: 2023-05-27 13:47:10 - 5000
categories: [remote-server]
tags: [ubuntu, linux, server]     # TAG names should always be lowercase
---
## Context
I have 2 computers. One of them is my PC (linux machine) and the other one is my laptop (an old macbook pro). I usually do the hardwork with my PC and use the laptop when I'm outside. The main problem is when I want to run something heavy in my laptop (e.g. training a deep learning network), I'd love to send the tasks to my PC from my laptop and let it do the hard job while I'm not at home. Technically, I want to set a server/client architecture, where my server is my linux machine and the client is my laptop.

__Equipment__
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
The blue highlighted is the IP address. Be aware it can change over time.
{: .prompt-tip }


### 1.2 Configuration in laptop (client)
> This configuration is done using a unix based system (macbook pro). if you are in Windows, you should check out other ways to enable an SSH client such as PUTTY.
{: .prompt-warning }

macOS comes with a built-in SSH client so you'll just need to type the command ```ssh```. Open the terminal and run the following command with the user and IP address found in previous step.
```bash
ssh <user>@<IP address of server>
```
It will ask for your password and then you'll be connected to your linux server:

![connection image](/assets/images/remote-server/mac-connection.png "connection image")


## 2. Remote configuration
First, be sure that you are able to connect to the linux machine following the first section.

Now, in order to connect from any place outside from your network (e.g. from a cafe, university, etc.) you must do some extra steps. These steps involve working mostly with the router's configuration.

> In order to access the configuration of your router, you can do it by typing it's IP address in the browser. To know the IP address of a router, run the next command in your linux machine,
```bash
ip route
```
{: .prompt-tip}

### 2.1 Port forwarding
What is port forwarding? Port forwarding is a way of allowing people from the internet to connect to you. 

You need to configure port forwarding on your home router. This involves specifying that incoming SSH traffic (usually on port 22) should be directed to your Linux machine's internal IP address.

>The idea behind this is: When you connect from your university, you will initiate an SSH connection to your home network's public IP address. Then, the router will receive this incoming connection request on port 22 and redirect it to your linux machine.
Note this is similar with what you've done in the first part, but instead of connecting to your linux machine directly with it's IP address, you are using the IP address of your home network as an intermediary.
{: .prompt-info }

1. __Set a static IP address to your linux machine:__ The internal IP address you found in the first part can vary over time. If you do port forwarding with that IP, when it changes you won't be able to connect through the router because it could be redirecting to a different device. Therefore, you must be sure the IP doesn't change over time. To do this, you can set a DHCP reservation. That is, telling the router you want to reserve an IP address to your linux machine. But how the router knows which is the linux machine? well, the router knows with the MAC address. Every network device has a MAC address and it is an unique identifier(and rarely changes). In other words, you are telling the router that whenever your linux computer is connected to your home network, assign the IP address.

    - Get the MAC address of you linux machine: Using the following command, 

        ```bash
        ifconfig | grep ether | awk '{ print $2 }'
        ```
        it return the MAC address (highlighted):

        ![MAC adress image](/assets/images/remote-server/MAC-address.png "MAC address image")<br>

    - Make DHCP reservation:
3. __Port forwarding:__
    see youtube
### 2.2 Connection


## 3. Optional setup

### 3.1 Setting DDNS

### 3.2 Save SSH session in macOS
Kind of a pain typing in the command the user and public IP address every time you want to connect to your server. But you can save this credentials in a file named config:
- Go to the folder .ssh and create a file called config:
```bash
cd ~/.shh
nano config
```
- Copy and paste:
```bash
Host sauron
HostName 192.168.1.14
User daguirre
```
Edit as you require: HostName is the IP address you are trying to connect, User the user you provide to log in the linux machine and Host is how you want to name that connection (yes, I love Lord of the Rings...)

Now, instead of connecting as mentioned in section 1.2, you can do it with simply the Host: 

```bash
ssh sauron
```