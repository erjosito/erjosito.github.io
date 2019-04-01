---
layout: post
title: Make Your NSGs Dance (Dynamic Azure Network Security Groups)
date: 2017-08-28 15:07:36.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- arm
- azure
- functions
tags:
- azure api
- azure arm
- azure functions
- azure networking
- azure nsg
- azure security
- network security group
meta:
  _thumbnail_id: '1066'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/902170812026937344";}}
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '8716219271'
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2017/08/28/make-your-nsgs-dance-dynamic-azure-network-security-groups/"
excerpt: Find out how to build a solution on top of Azure APIs to automate the creation
  of NSGs.
---
As you probably know, Network Security Groups (NSGs) in Azure are an important security functionality in order to control network access to and from your virtual machines.

NSGs can be configured at two levels: at the subnet level and at the VM level. Or at both, if you prefer, and then they will be merged. The important thing to note is that they are \*\*always\*\* enforced at the VM level, so they provide a formidable security weapon, able even to filter traffic between two neighbor VMs in the same subnet.

However, if you were to configure one NSG for each VM, chances are that you would go crazy after a few weeks, as the number of NSG rules to manage grows exponentially, hence the possibility to have NSGs applied at the subnet level, to ease on that management effort. But in that case you would be imposing a security posture on Virtual Machines depending on the subnet they belong to, which does not seem to be flexible enough.

One quick example: you have two subnets, and a bunch of Windows and Linux VMs distributed across both subnets. You would like to allow only TCP port 22 to the Linux VMs, and only port TCP 3389 to the Windows VMs. How would you do it? Probably with NSGs assigned at the VM level. This simple ruleset is still easy to maintain, but as you keep adding things like Web servers, databases, and other components, managing the per-VM NSGs might become a daunting task.

I guess my colleagues in Redmond are already working to alleviate this problem, but in the meantime, you could just leverage Azure APIs to achieve a scenario where your NSGs are dynamic, with a little help of your Azure Functions friends.

Code first: I have put all the code of this solution in my Github repo [here](https://github.com/erjosito/dynamicNSG). It is essentially a way to define a sort of object groups (similar to firewall object groups), and having VMs dynamically belonging (or not) to those groups based on attributes such as their names, operative system or even ARM tags.

In the previous example you might just define two groups: "linux" (matching all VMs with a Linux OS) and "windows" (matching all VMs with a Windows OS). You would just define a policy allowing TCP port 22 to your group "linux", and TCP port 3389 to your group "windows".

A bunch of Azure Functions in the background would then dynamically build individual NSGs that you can then associate to each VM.

But let us continue with the previous example. What about our web and database servers? You could define a group "web" matching on VMs whose name include the string "web", and a group "database" matching on VMs whose name include the string "db". Note that the groups "linux"/"windows" and "web"/"database" could overlap, or not. It does not matter, the resulting NSGs will take into account the properties of your VMs and configure the NSGs accordingly.

You might say that this is all fine, but not dynamic enough. What if you want to modify the security posture of a VM on the fly? For example, what if you discover a security vulnerability in a certain VM? How to isolate it from the rest of your environment?

You could define a group called "lowsec" matching on the ARM tag "security" with the value "low". You would just need your security scanner setting the ARM tag of that VM accordingly, recalculating your NSGs, and voil├á, you have isolated that VM automagically from your production VMs.

This video shows what the prototype GUI I built with MVC looks like:

https://youtu.be/5f5MLrkoel4

And for better understanding, the following picture describes the overall architecture of the solution showcased in the video above:

![dynNSG_arg]({{ site.baseurl }}/assets/images/dynnsg_arg.png)

You will ask: is this code ready for production? Probably not, but this is an example of how to add functionality on top of Azure APIs, either for yourself or as a value-added for your customers, if you are a Microsoft Azure partner.

Next thing I want to check is how to automate the refreshing of the NSGs upon any change of the VM ARM tags, or any alert from Azure Security Center, probably leveraging the new Event Grid. Stay tuned!

