---
layout: page
title: Home Network & Lab Setup
permalink: /lab/
---

This page is here to provide context to anyone who may be interested. I will do my best to keep 
this page up to date as I change hardware or software.

**Important Note**
> None of the equipment I am running is required to practice what I am posting here. To that 
point, running this hardware and software can present issues and complications. I cannot 
stress enough that you should use what you are comfortable with using and supporting. If your lab 
is too difficult or complicated to use, you'll probably never use it.


## Home network

My home network VMs are running on an old Dell server running the free version of ESXi.

- Networking:
    - Wired:
        - Cisco 3750G, 48 port
    - Wireless:
        - Cisco vWLC (5 licenses)
        - 3 x Cisco 3502i APs
- Firewall:
    - pfSense virtual firewall


## Lab

My lab servers are both running Proxmox and are configured as a cluster for ease of management. 
I use the first server for a persistent Kali VM, a pfSense firewall for the lab, and for any 
vulnerabile VMs that I want to play with. The second server is used to run a Minecraft server.

Current lab Equipment:
1. 2U Rackmount Dell server
    - 2 x Intel X5560 Xeon, 4 core, 8 thread (16 vCPUs total)
    - 74 GB memory
2. 2U Rackmount Dell server
    - 2 x Intel X5530 Xeon, 4 core, 8 thread (16 vCPUs total)
    - 16 GB memory
