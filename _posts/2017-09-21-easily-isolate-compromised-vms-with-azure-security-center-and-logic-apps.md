---
layout: post
title: Easily isolate compromised VMs with Azure Security Center and Logic Apps
date: 2017-09-21 11:03:35.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- arm
- azure
- functions
tags: []
meta:
  _thumbnail_id: '1204'
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '9512127105'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/910806713300856833";}}
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2017/09/21/easily-isolate-compromised-vms-with-azure-security-center-and-logic-apps/"
---
Lateral movements are typically the first thing that hackers do after compromising a system in order to spread their attack to other valuable targets, so isolating systems that have been compromised is of paramount importance for an organization from a security perspective.

In a previous blog ([here](https://1138blog.wordpress.com/2017/08/28/make-your-nsgs-dance-dynamic-azure-network-security-groups/)) we saw a possibility of having abstract policies in Azure such as "_permit SSH to all my VMs running a Linux OS_" or "_permit HTTP to all my VMs whose name contains the string 'web'_", and having those policies dynamically evaluated in real time. A very interesting application of this sort of dynamic policies is for quickly isolating machines that have been compromised.

Essentially, you could define a rule that says something like "_deny all traffic for VMs tagged as 'Compromised'_". Now the only thing you need to do is detecting an attack, setting the corresponding tag on the attacked VM (to flag it as compromised), and regenerating the NSGs out of the security policy. Easy, right?

Azure Security Center can detect many of the security problems that can appear on a VM, and it even automatically remediates some of them. I actually had some trouble to test this, since when autoremediation kicks in (such as to remove malware-infected files I placed on my VMs), the user does not need to do anything, since no remediation is required any more.

But let's assume that Azure Security Center finds a security threat, and it requires that the user does something about it. Today a very interesting feature is in preview that allows the user to kick off Logic Apps (called "playbooks" in Azure Security Center) to remediate security issues (note the "_Run Playbooks_" button, you can find the documentation for this feature┬á[here](https://docs.microsoft.com/en-us/azure/security-center/security-center-playbooks)):

![ASC_LogicApps_Button]({{ site.baseurl }}/assets/images/asc_logicapps_button.png)

That is mostly all what we need. Well, besides a very simple Logic App that calls an Azure Function to set the 'compromised' tag for the VM in question, and another one to recalculate the NSGs. Easy peasy, right?

You can see a demo of what all this looks like in this short 5-minute video:

https://youtu.be/L7P\_NwCO4do

Besides that, I will be posting the source code into [this repo](https://github.com/erjosito/dynamicNSG), if you feel like reusing this stuff.

What do you think? Do you have further ideas to automate remediation actions with Azure Security Center and Logic Apps?

