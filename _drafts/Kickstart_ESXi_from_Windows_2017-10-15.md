---
layout: post
title: Kickstart ESXi from Windows Server 2012
---

A Kickstart is just a method used to boot an ISO from some form of media.  In my reading about Kickstart systems the main thing that keeps being repeated is, do all this in Linux.  Take this version of Linux (usually Red Hat or CentOS) and build this Kickstart server.  Let me say this right now, I love the idea of running a Linux system to do this sort of task.  

However, for me and most of my colleagues on my team at work there would be a steeper learning curve with Linux.  I decided to take a different turn at the “Choose your OS” step.  I went with Windows Server 2012 R2.  I may still end up using a Linux Kickstart system for the related project.   

The goal of this part of the project: Install ESXi on HPE hardware using some automated method.  This is a simple goal in form but a very loaded statement.  I tried VMware AutoDeploy and had a certain amount of success with that product.  However, in my opinion, there is a lot of “scaffolding” to make AutoDeploy work the way it is intended.  This is less than desirable in my environment for various reasons.  Kickstart on Windows, it might sound more complicated than it actually is.  

Show me the outline:

-What we need:

--1. Pick an Operating Systems – Windows Server 2012 R2
--2. PXE Boot System? – TFTP32 (or 64-bit)
-----a. TFTP Server service 
-----b. DHCP Server service 
--3. Get Linux boot file - gPXElinux.0 – Get this from a special download site
--4. Select how to present Kickstart file (and possibly other files) – NFS
--5. Select flavor of ESXi – I chose HPE ESXi 6.0 Update 2 – Most physical VMware hosts in my environment will be HPE Proliant G9 (1U-Pizza-box)
