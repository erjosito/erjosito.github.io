---
layout: post
title: The network is *not* in my way
date: 2015-02-11 19:46:00.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories: []
tags:
- automation
- ciscoaci
- f5
- network automation
- sdn
meta:
  tumblr_erjosito_permalink: http://erjosito.tumblr.com/post/110742308847/the-network-is-not-in-my-way
  tumblr_erjosito_id: '110742308847'
  _wp_old_slug: '110742308847'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2015/02/11/the-network-is-not-in-my-way/"
---
I recently got from F5 colleagues the idea, why not working on an automation use case showing the integration between ACI and F5. One use case they see often in their customer basis is autoscaling of Web farms: some system is looking at the load on the servers, and if that load grows over a certain threshold, new servers are provisioned. If the load falls again, the servers provisioned in the previous step are decommissioned.

This is one of the typical scenarios that trigger the statement ÔÇ£the network is in my wayÔÇØ: I can deploy new (virtual) Web servers very easily, but bringing them into the network and the load balancing algorithm can take much longer. However, with the integration between ACI and the ADC, I went through a very interesting experience: I just needed 5 minutes to figure out how to bring new servers into the network, but I needed much longer to bring up the VMs. Long story short: it is all about APIs: ACI offers today a single API to manage your L2-L7 network (including ADCs).

<figure class="tmblr-embed">[youtube https://www.youtube.com/watch?v=Luj2ulYAvGk?feature=oembed&amp;enablejsapi=1&amp;origin=https://safe.txmblr.com&amp;wmode=opaque&amp;w=500&amp;h=374]</figure>

Bringing new servers to the network has 2 steps (the ÔÇ£VM partÔÇØ and the ÔÇ£network partÔÇØ): new VMs should be brought up with the right port group (cloning a template should take care of that), and provided the new VM gets a new IP address over DHCP for example, this new IP address should be configured in the load balancer. I decided to start with this last part, what I thought it would be the most difficult one.

To my surprise, I found out that the network part was REALLY easy! I turned on the ÔÇ£API inspectorÔÇØ in ACI, which is a tool that shows you which API calls you need to run in order to replicate what you do in the GUI. In the GUI I added and removed a server to an existing Web farm, and the API inspector showed me which API calls to do for that: 3 calls for adding a server to the farm, 1 call to remove the server. And a login call before that. Period.

I put those calls into my favourite REST client (I love Paw), and there you go! One of the reasons why I like Paw is because it can translate REST calls to your favourite programming language (which happens to be Python in my case), so writing Python functions to add / remove servers from the farm was a piece of cake.

That was easy! I have to say I struggled a bit with the ÔÇ£easy partÔÇØ of this process, which is creating the VM. For that I have used the pysphere Python library, that offers a way to monitor the CPU usage of the guest OS in a VM (only updated every 60 seconds, so donÔÇÖt expect immediate action!).

Lastly, to increase the CPU utilisation of the farm servers I am using the basic bash command

> dd if=/dev/zero of=/dev/null

We will use the CPU load as indicator of when new servers need to be deployed in the farm.

So now we have everything: the script should run an infinite loop where the CPU of VMs are verified. If the CPU load exceeds a predefined threshold, a new VM is spawned, and its IP address is added to the Web farm over APICÔÇÖs ACI. When the CPU falls again, the VM will be deleted and the IP address will be removed from the server farm.

My lesson learnt with this exercise was that the network in this case could be deployed quicker as the VMs were instantiated. Actually quicker, in the script you can see wait times built in order to wait for the new VMs to boot. The network is not in my way any more.

If you are interested, here is the script I have been using in my example. Please donÔÇÖt expect to find Python best practices here, this is more a ÔÇ£quick and dirtyÔÇØ implementation: [https://github.com/erjosito/stuff/blob/master/cpu\_monitor.py.](https://github.com/erjosito/stuff/blob/master/cpu_monitor.py.)

&nbsp;

