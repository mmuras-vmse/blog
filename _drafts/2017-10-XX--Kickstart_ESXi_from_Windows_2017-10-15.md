---
layout: post
title:  Kickstart ESXi from Windows Server 2012
---

2017-10-Xx

A Kickstart is just a method used to boot an ISO from some form of media.  In my reading about Kickstart systems the main thing that keeps being repeated is, do all this in Linux.  Take this version of Linux (usually Red Hat or CentOS) and build this Kickstart server.  Let me say this right now, I relish the idea of running a Linux system to do this sort of task.  

However, for me and most of my colleagues on my team at work there would be a steeper learning curve with Linux.  I decided to take a different turn at the “Choose your OS” step.  I went with Windows Server 2012 R2.  I may still end up using a Linux Kickstart system for the related project.   

The goal of this part of the project: Install ESXi on HPE hardware using some automated method.  This is a simple goal in form but a very loaded statement.  I tried VMware AutoDeploy and had a certain amount of success with that product.  However, in my opinion, there is a lot of “scaffolding” to make AutoDeploy work the way it is intended.  This is less than desirable in my environment for various reasons.  Kickstart on Windows, it might sound more complicated than it actually is.  

### Show me the outline:

1. Copy of VMware instructions on Building a Kickstart server
2. Pick an Operating Systems – Windows Server 2012 R2
3. PXE Boot System? – TFTP32 (or 64-bit)
4. DHCP Serer? TFTP32 (or 64-bit) handles that also
5. Get Linux boot file - gPXElinux.0 – Get this from a special download site
6. Select how to present Kickstart file (and possibly other files) – NFS
7. Build Kickstart script file - Get basic examples and modify
8. Select flavor of ESXi – I chose HPE ESXi 6.0 Update 2 


NOTE: Most physical VMware hosts in my environment will be HPE Proliant G9 (1U-Pizza-box)

So what now?

### Well... first things first, lets go get our items on the grocery list from above.

1. !--GOLD MINE--! [VMware's documentation on building a kickstart system](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/techpaper/vsphere-esxi-vcenter-server-60-pxe-boot-esxi.pdf) is really well done, but it requires a very careful and close read.  Essentially, this document will walk you through a Linux kickstart if you are an everyday Linux user.  Lucky for me, I have used it enough Linux from a previous life somewhere to be able to stumble and fumble my way through it. 

2. Build Windows Server 2012 R2 VM - I will not be going into the details of this any further.

3. Get [TFTP32 from here](http://tftpd32.jounin.net/tftpd32_download.html) - I am using the 64-bit version.

4. Oh wait, (smack!) TFTP (mentioned in 3 above) will take care of both TFTP and DHCP (and give you the platform for PXE booting)

5. Get pxelinux.0 [pxelinux file from here](https://www.kernel.org/pub/linux/utils/boot/syslinux/Testing/3.86/) I have also read other peoples notes that say versions later than Syslinux 3.86 will be problematic.  VMware's docs (see 1 above) calls out Syslinux 3.86.

6. For NFS (my choice) to present the ks (kickstart file), I chose NFS because: a. Windows Server 2012 R2 has the NFS Server as a Native Service AND b. because I may need it for another ISO and kickstart later on
!--BONUS--! Here is a pretty straight forward look at how to do setup NFS on Windows 2012 by [Shane Rainville](http://www.serverlab.ca/tutorials/windows/storage-file-systems/configuring-an-nfs-server-on-windows-server-2012-r2/)

7. VMware provides some nice ks (kickstart file) examples and ground work here on [KB-X234](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2004582).  
!--BOUNS--! For more ks (kickstart file) look at this [website by William Lam for examples](http://www.virtuallyghetto.com/2014/10/how-to-automate-vm-deployment-from-large-usb-keys-using-esxi-kickstart.html)

8. Download HPE OEM ESXi from VMware (or chose some other reliable source)...translation: Vendor Download page?

### Jumping into the mess at TFTP32 / TFTP64 utility

From this point, I am assuming you have built your Windows 2012 R2 VM, and you have downloaded all necessary docs and software.

For the TFTP64 Settings > Global tab:

1. Enable TFTP Server
2. Enable DHCP Server
3. All other services are optional

![alt text](http://mmuras-vmse.github.io/images/2017-10-15_kickstart/TFTP-Settings-Global.png "Global tab")

For the TFTP64 Settings > TFTP tab:

1. Set your Base Directory
2. TFTP Security - (your milage may vary) best rule of thumb - set to None and increase and test it as you increase it to make sure system is functioning properly.
3. Advanced TFTP Options -> Uncheck option 1, check option 2 - 6.  NOTE: I consider "PXE Compatibility" option 2.  (see picture)

![alt text](http://mmuras-vmse.github.io/images/2017-10-15_kickstart/TFTP-Settings-TFTP_noIP.png "TFTP tab")

For the TFTP64 Settings > DHCP tab:

![alt text](http://mmuras-vmse.github.io/images/2017-10-15_kickstart/TFTP-Settings-DHCP_noIP.png "DHCP tab")

### Unpacking gPXELinux.0 file
This is a direct picture from VMware's docs that I called out earlier.  I also added the pointer to the Syslinux location. 

You definitely need to unzip which ever package you choose, and most importantly you need the gPXELinux.0 file.  

NOTE: You may also end up needing other files if you do more customizations, so its a good package to have sitting around somewhere handy.

![alt text](http://mmuras-vmse.github.io/images/2017-10-15_kickstart/Get-PXELinux0-file-2.png "Global tab")

The screen shot from the VMware document (above) goes into some detail about where to place the gpxelinux.0 file .  However, for a little more 
clarity on my system this is how it looks...

![alt text](http://mmuras-vmse.github.io/images/2017-10-15_kickstart/Path_for_gpxelinux.0.png "Placing the gpxelinux.0 file")

This directory contains 3 different types of items:

  a. gpxelinux.0 file
  b. pxelinux.cfg directory
  c. ISO File directory 

### Configure NFS on Windows Server 2012
Setup NFS on Windows 2012 by [Shane Rainville](http://www.serverlab.ca/tutorials/windows/storage-file-systems/configuring-an-nfs-server-on-windows-server-2012-r2/)

This is basically handled in two (or three) sections...depending on how you look at this.

  a. Install Services for NFS
  b. Configure the NFS Share (where clients will connect to)

The biggest Stumbling block for me when working with NFS, was getting the sharing to behave as I wanted.

![alt text](http://mmuras-vmse.github.io/images/2017-10-15_kickstart/NFS_Advanced_Sharing.png "NFS Sharing for the Kickstart file directory")