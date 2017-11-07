---
layout: post
title:  Beyond user driven ESXi installs: Kickstart vs. AutoDeploy
---

2017-11-06

I work for a company, on a team Architects which recently started to deploy servers with VMware ESXi and VMs for production builds on a larger scale.  The way we install is by ISO on USB or by ISO from iLO virtual CD.  This is not a bad way to go if you have 5 or 10 servers.  This is a problem, as USB installs is not sustainable for the company long term with the number of hosts we want to eventually deploy.  

So my goal of this part of the project: Install ESXi on HPE hardware using some automated method.  There are some reasonable options available but the two likely candidates for my team are AutoDeploy or use a Kickstart system.  

I tried VMware AutoDeploy and had a certain amount of success with that product.  However, in my opinion, there is a lot of “scaffolding” to make AutoDeploy work the way it is intended.

I tried Kickstart (booting from TFTP/PXE) so this would work very similar to AutoDeploy in certain ways.  However, with Kickstart, I have extra options.  I chose to let my Kickstart system function as a more self-contained utility (all services/resources on one system).  I could have also chosen have my services/resources spread out.

Basically it comes down to cost and complexity and lack of flexibility, in one way or another, AutoDeploy can get expensive for my environment.  Either we need a vCenter running a location where we did not have one before, or we have to effectively transfer entire ISOs over the wire.   AutoDeploy would also require running multiple systems for the company if we add the vCenter Server at another location.  
The idea of being able to pick and choose what you want from checkboxes (see AutoDeploy) is fantastic way to manage ESXi hosts.  However, there are other ways to do this that don’t require you to buy an extra license for vCenter.  And best of all (for me anyway), I get to learn a lot more PowerShell and PowerCLI.

At this point, I am not going to write off AutoDeploy (especially since we are still early in our process), but for now it looks like using a Kickstart system will be better suited for our needs in our Environment.  We have some related projects on the horizon that may actually warrant us taking the plunge and spending funds to go with AutoDeploy.  No matter which way you go –AutoDeploy or Kickstart, if you are going to be deploying a large number of hosts, you should consider one of these to make your ESXi host builds faster and more consistent.
