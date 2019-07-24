---
layout: post
title: Router - First Line of Defence
---

# Router - First Line of Defence

> *definition* : **A router is a networking device that forwards data packets between computer networks**.

```text
------------------------
```

If the internet is likened to a war-zone then the home router sitting between your digital devices and ISP[^1] is equivalent to the first line of defence, under your own control, against invasion and ruin! 

> 

Given the obvious importance of protecting these digital devices it makes perfect sense that this primary piece of hardware be both well understood and configured for maximum protection and ease of use.

```text
------------------------
```

The updatability of a router's firmware is of primary importance in my opinion given that exploits and bugs inevitably surface over time. 

>

I've been using [Asus](https://www.asus.com/us/) network gear and motherboards for some time now and have yet to have occasion to complain.

> 

[Asuswrt-Merlin](https://www.asuswrt-merlin.net/) is a [popular](https://www.snbforums.com/forums/asuswrt-merlin.42/) third party firmware offering tweaks and fixes capable of seamlessly replacing Asus' default [Asuswrt](https://www.asus.com/ASUSWRT/) firmware.

>

<a href="https://www.asus.com/us/Networking/RTAC68U/gallery/"><img src="/images/01/router-1.jpg"/></a>


> * my current home gigabit dual wan wifi router - the **Asus RT-AC68U** 


```text
------------------------
```

# Router - My LAN Defaults

>

My ISP provides me with a 4 port [optical network terminal](http://www.gpon.com/home) with access to two ip addresses. *LAN1* on this device is attached to the RT-AC68U's blue **WAN** port (primary wan) and similarly *LAN2* to the RT-AC68U's yellow **LAN1** port (secondary wan). 

>

I use the RT-AC68U's dual wan feature to [load balance](https://en.wikipedia.org/wiki/Network_Load_Balancing) the two ip addresses and DHCP is disabled by default for added security. Each device in the network is assigned a static IP address and routed to either the primary or secondary dual wan unit. 

>

The two PCs in my home office are attached to the RT-AC68U's LAN2 (192.168.1.111) and LAN3 (192.168.1.112) ports respectively.

>

Good quality [cat6](https://en.wikipedia.org/wiki/Category_6_cable) cables were used to connect all wired devices and the RT-AC68U's default asuswrt firmware is manually replaced with Merlin's [latest](https://sourceforge.net/projects/asuswrt-merlin/files/RT-AC68U/Release/) update from sourceforge.net.

>

An [ssh key pair](https://wiki.archlinux.org/index.php/SSH_keys#Generating_an_SSH_key_pair) was created and added to the administration section of the router's web gui and linked to a non default ssh port to facilitate a password free router login experience on the terminal. 

>

```bash
$ ssh-keygen -b 4096 -f ~/.ssh/clarke-rtam -C "clarke@rtam"
$ xclip -sel clip < ~/.ssh/clarke-rtam.pub
$ ssh -p <ssh_port> <login_name>@rtam.home
```

* clarke-rtam.pub pasted to:

>

   [ *router:webui>administration>system>service>authorized keys* ]

>

The RT-AC68U's wifi antennae allow for wireless connections to two android smartphones (192.68.1.113/114) and one wireless printer (192.168.1.115).

```text
------------------------
```

```bash
$ cat /etc/hosts
   127.0.0.1 localhost
   127.0.0.1 ads.google.com
   127.0.0.1 analytics.google.com
   127.0.0.1 doubleclick.net
   127.0.0.1 google-analytics.com
   127.0.0.1 googleadservices.com
   127.0.0.1 googlesyndication.com
   127.0.0.1 googletagmanager.com
   127.0.0.1 ssl.google-analytics.com
   127.0.0.1 www.google-analytics.com

   192.168.1.1      rtam.home rtam   # Asus RT-AC68U
   192.168.1.111    anbc.home anbc   # Asus H270F MB PC1
   192.168.1.112    mmnc.home mmnc   # Asus H270F MB PC2
   192.168.1.113    acmp.home acmp   # Samsung S4 I9500 PHONE1
   192.168.1.114    mcmp.home mcmp   # Samsung S4 I9500 PHONE2
   192.168.1.115    hpdw.home hpdw   # HP M402dw
```

```text
------------------------
```

A 64GiB USB pen drive is attached to the RT-AC68U's usb 3.0 port to facilitate running [custom](https://diversion.ch/amtm.html) scripts.

> 

```bash
$ ssh -p <ssh_port> <login_name>@rtam.home "amtm"
```

>

![amtm : asuswrt-merlin terminal menu](/images/01/router-2.jpg)

```text
------------------------
```

# Router - Self Signed Certificates

>

[mkcert](https://github.com/FiloSottile/mkcert) was installed and used to create a self-signed private key and SSL certificate to enable https access to both the router and printer GUIs: 

>

```bash
$ yay -S mkcert
$ mkdir ~/.config/local-certs
$ cd ~/.config/local-certs
$ mkcert clarke.home anbc.home mmnc.home acmp.home mcmp.home hpdw.home rtam.home localhost 127.0.0.1
$ openssl pkcs12 -inkey clarke.home+8-key.pem -in clarke.home+8.pem -export -out clarke.home+8-key.pfx
```

>

The following generated keys were then imported into either the router or printer webui:

> 

* Router  : Private Key ; ~/.config/local-certs/clarke.home+8-key.pem
* Router  : SSL Certificate ; ~/.config/local-certs/clarke.home+8.pem
* Printer : Certificate ; ~/.config/local-certs/clarke.home+8-key.pfx
	
```text
------------------------
```
	
# Router - Network Map

>

In the scenario screenshot below both PCs are turned on and connected to the internet - the default workday situation.

>

The wifi connection is set to scheduled mode and is currently timed to be disabled. Simple bash scripts allow for altering this wifi mode between scheduled, off and on.

>

[Macchanger](https://wiki.archlinux.org/index.php/MAC_address_spoofing) is aso integrated into the above wifi bash scripts which allow changing the external ip and the mac addresses on demand

```text
------------------------
```

* Network overview showing dual wan connections.

>

<a href="/images/01/merlin-1.jpg"><img src="/images/01/merlin-1.jpg" width="600" /></a>

>

```text
------------------------
```

* Primary WAN unit assigned for PC1, Phone1 and printer connections.

>

<a href="/images/01/merlin-2.jpg"><img src="/images/01/merlin-2.jpg" width="600" /></a>


```text
------------------------
```

* Secondary WAN unit assigned for PC2 and Phone2 connections.

>

<a href="/images/01/merlin-3.jpg"><img src="/images/01/merlin-3.jpg" width="600" /></a>

>


```text
------------------------
```

* Dual WAN unit assignments

>

<a href="/images/01/merlin-4.jpg"><img src="/images/01/merlin-4.jpg" width="600" /></a>

>

```text
------------------------
```

* router local self-signed SSL certificate

>

<a href="/images/01/merlin-5.jpg"><img src="/images/01/merlin-5.jpg" width="600" /></a>

>

```text
------------------------
```

* https access to router webui

>

<a href="/images/01/merlin-6.jpg"><img src="/images/01/merlin-6.jpg" width="600" /></a>

>

```text
------------------------
```

* https access to printer webui

>

<a href="/images/01/merlin-7.jpg"><img src="/images/01/merlin-7.jpg" width="600" /></a>

>

```text
------------------------
```
# KEY

[^1]: [Internet Service Provider](https://en.wikipedia.org/wiki/Internet_service_provider)
