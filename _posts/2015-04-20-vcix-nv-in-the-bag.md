---
layout: post
title: VCIX-NV in the bag!
date: 2015-04-20 19:39:24.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories: []
tags:
- network virtualization
- nsx
- vmware
meta:
  tumblr_erjosito_permalink: http://erjosito.tumblr.com/post/116932726122/vcix-nv-in-the-bag
  tumblr_erjosito_id: '116932726122'
  _wp_old_slug: '116932726122'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2015/04/20/vcix-nv-in-the-bag/"
---
Oh yeah, another 4-letter acronym in the CV! Today I am very happy to have passed the VCXN610 exam, the final obstacle before achieving the [VCIX-NV cert](https://mylearn.vmware.com/mgrReg/plan.cfm?plan=52165&ui=www_cert).

After all the study and lab time, I have gained some experience with NSX for vSphere, and I would like to summarise here some of my thoughts about this technology.

**The good**

VMware has made a good job of integrating multiple technologies like NAT, load balancing, dynamic routing protocols or firewalling, to mention a few, in a single GUI (vCenter).

The installation process works quite nice for the most part (except maybe when configuring VXLAN, where rolling back or changing the VTEP settings can be quite time-consuming).

The Hands-On-Labs from VMware are awesome! In less than one minute anybody can have an NSX lab ready to be used. There are 2 NSX labs there,┬áÔÇ£NSX introductionÔÇØ and┬áÔÇ£NSX advancedÔÇØ. Both are pretty good.

**The bad**

Something I donÔÇÖt like that much is the lack of choice about the networking technologies. For example, as compared to troubleshooting of traditional routers the products based on the old vShield Edge are quite rudimentary. I had this problem where my physical router was configured with the wrong MTU. I tried to find the problem at the NSX side during hours, until I went to my physical router and activated┬áÔÇ£debug ip ospf adjacencyÔÇØ. Bingo! WouldnÔÇÖt it be great if you could have your Cisco CSR 1000v or your Brocade Vyatta virtual router instead of the ESG, for those who wanted it?

The lack of what I call an┬áÔÇ£API recorderÔÇØ: working with the API and the API reference guide (a PDF document of around 400 pages) is extremely painful. It would be great something like what products like Cisco UCS or ACI offer, or even Openstack (from the CLI with the ÔÇôdebug option, not from the Horizon GUI though) offer: clicking in an object and seeing its ID, or doing some operation on the GUI and seeing somewhere which API call would achieve the same objective.

**The ugly**

Everybody speaks about the lack of coherence between the virtual and physical layers. I tend to agree here, mostly from a management perspective: since all NSX is managed from vCenter, my take is that in most organisations it will be the vSphere admin the one in charge of it. Whereas the physical network stays in control of the Network admin. Here is where I see the biggest problem: network management fragmentation. Obviously, in a smaller company where the network and the vSphere admin are the same person, this would be a non-issue.

Another thing that drove me mad is the different interfaces you need to work on, especially for troubleshooting. While┬áÔÇ£normal operationsÔÇØ are run from vCenter, when troubleshooting you have other tools you need to be aware of: the CLI of the controllers, the CLI of the ESX hosts, the CLI of the ESG, the CLI of the distributed routerÔÇª And of course, most of them with completely disparate looks and feels.

And finally, I think the idea of bringing everything under one single tool (vCenter) will slow down feature velocity and impair quality. Why am I saying this? In the past I have seen some products from different vendors trying to do too much (and yes, at Cisco we had our share of that too): they would implode after getting too big. You can start seeing this in NSX in the dependencies across multiple functions: if you do bridging you cannot do routing, if you do ECMP you cannot do firewalling, etc. The more complex products get, the more dependencies you will see. You could counter that modern software practises are now able to do so much more than in the past. Time will tell.

In my opinion modular solutions work better: small functional islands that offer consistent, easy-to-use APIs, and that can be enhanced by solutions from the same vendor (the easy way), or from other vendors (maybe not so easy, but still possible).

All in all, I had a good time playing with NSX, I would recommend network folks to go and do the same, and disagree with me at some point!

