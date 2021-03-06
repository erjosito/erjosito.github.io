---
layout: post
title: 'Best Kept Secret in the SDN Industry: HNV'
date: 2017-09-29 11:40:13.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- azure
- vmware
- vxlan
- windowsserver
tags: []
meta:
  _thumbnail_id: '1316'
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '9789835182'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/913715038560690176";}}
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2017/09/29/best-kept-secret-in-the-sdn-industry-hnv/"
---
If you are wondering what HNV stands for, you are exactly right in this blog. HNV, or Hyper-V Network Virtualization is how Microsoft used to call their virtual networking stack in Windows Server. Nowadays it is usually referred to as Windows Server SDN, or more accurately Windows Server SDN v2 ("v2" meaning the newer implementation in Windows Server 2016, as opposed to "v1" for Windows Server 2012 R2).

With Windows Server SDN you have at your disposal an SDN implementation that essentially **costs $0** (provided you already have Windows Server licenses) and potentially **scales to the infinite** (it is the technology used in Microsoft Azure, "infinite" here means "up to Microsoft's datacenter sizes").

I have to confess that I personally prefer the term HNV, since it more accurately describes what it is, as opposed to one of the most washed down terms in the IT industry today. However I do not work in Marketing, so for the sake of consistency I will be using the official "SDN" naming convention, even if it hurts my feelings.

You could compare Windows Server SDN to the far better known VMware NSXv. It is a VXLAN-based technology that implements overlay networks using the hypervisor virtual switch as VTEPs. Huh? In other words, it allows to build logical networks for your VMs across your datacenter without having to reconfigure your physical switches, by using tunnels between Hyper-V hosts. It additionally provides some services on those logical networks such as **packet filtering** (anybody heard the term microsegmentation?) or **load balancing**.

Both implementations are similar in which they are both based on additional networking functionality implemented natively in the hypervisor virtual switch. In the case of Microsoft Server SDN, this implementation is based on the Virtual Filtering Platform (VFP) extension to the Hyper-V virtual switch. This extension not only adds filtering capabilities to the virtual switch as it name seems to imply, but additional features such as L3 routing or NAT as well (more details about the Windows Server SDN architecture┬á[here](https://docs.microsoft.com/en-us/windows-server/networking/sdn/technologies/hyper-v-network-virtualization/hyperv-network-virtualization-technical-details-windows-server)).

Now there is an important distinction with most hypervisor-based network overlays out there: the VFP extension has been actually engineered for hyperscale in a public cloud, in this case Microsoft Azure, and that is driving some important aspects of the architecture. You can read more about VFP in this [great paper by Daniel Firestone](https://www.usenix.org/system/files/conference/nsdi17/nsdi17-firestone.pdf), or watch a presentation on it [here](https://www.usenix.org/conference/nsdi17/technical-sessions/presentation/firestone). This architecture is what grants Windows Server SDN its **unprededented scalability**.

For example, the **load balancing** architecture in Windows Server SDN is fundamentally different to NSX, where load balancing is provided by a virtualized network function (a derivate of HA-Proxy last time I had a look). In Window Server SDN load bancing is inspired in the load balancing design for Microsoft Azure, code named "Ananta". Read more about Ananta in this [SIGCOMM 2013 paper](http://conferences.sigcomm.org/sigcomm/2013/papers/sigcomm/p207.pdf). Essentially load balancing functions are spread over multiple components for better scalability: NAT and healthchecking is performed directly in the hypervisor virtual switch, and you have an additional VM-based stateless component (called SLB MUX) that scales horizontally. This SDN MUX instances speak upstream BGP with your infrastructure to advertise the VIPs, and downstream they send the traffic flows to one of the Hyper-V hosts.

**Multitenancy** is another one of the key differences of Windows Server SDN as opposed to VMware NSXv. You can think of VNets (aka Virtual Networks) as of VRFs if you are a network guy, or as of a Virtual Private Datacenter if you are an AWS guy, or well, as of a VNet if you are an Azure guy. Vnets are embedded in the Windows Server SDN infrastructure, and they represent the basic construct required to deploy a tenant's network. Vnets will then contain subnets, and vNICs will be assigned to subnets. IP addresses cannot overlap inside of a Vnet, but you can reuse the same IP address across multiple Vnets (as long as you are not connecting them to each other, obviously).

Even though the Azure heritage has a lot of advantages as explained above, it also has one negative aspect: implementing and managing Windows Server SDN is not as **user-friendly** as other enterprise-oriented SDN solutions. If you are a Powershell person you will feel at ease, if you are a VMM person, you will find yourself resorting to Powershell in some cases for tasks that are not supported by VMM just yet. There has been an extremely good automation effort for Windows Server SDN deployment in [this Github repo](https://github.com/Microsoft/SDN), so my hope is that in a near future these scripts will be natively integrated in System Center VMM in the near future.

Now if Windows Server SDN is so awesome, why haven't you heard about it so far? Well, one reason is that Microsoft is not getting rich with it (remember the $0 price tag?), so not a lot of marketing dollars are invested either. Another reason is the overall "cloud first" strategy of Microsoft's: if you are thinking about cloud, you'd better read about **Azure and Azure Stack** (which by the way use internally a flavor of Windows Server SDN), than about Windows Server SDN. And that makes sense, so that you can concentrate on more important aspects of your IT rather than on deploying network controllers. However, since Azure Stack is pretty new, there might be some situations where Windows Server SDN comes handy to build a private or a hybrid cloud.

Are you using Windows Server SDN in production? Have you played with it? Please add your comment below!

