---
layout: post
title: Was network management ever so cool?
date: 2015-04-09 21:00:40.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- n9k
- nxapi
- vxlan
tags: []
meta:
  tumblr_erjosito_permalink: http://erjosito.tumblr.com/post/115968229767/was-network-management-ever-so-cool
  tumblr_erjosito_id: '115968229767'
  _wp_old_slug: '115968229767'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2015/04/09/was-network-management-ever-so-cool/"
---
Admittedly, I am sick. That is at least what most people would say, if I told them that last night I was having fun programming in Python in order to automate a VXLAN environment using an EVPN control plane. Whaaaat???

In my opinion, VXLAN fabrics with EVPN are really cool. Lots of benefits over traditional designs: scalable, IP-only backbone, standards-based, with optimal forwarding (routing in the ToR switch), and I am probably forgetting some.

One issue though is that the configuration gets more complex. Creating tenants and networks gets more complex as your grandpaÔÇÖs VLANs and SVIs with Spanning Tree.

A colleague of mine asked me this week whether I had any Python script to help alleviate this configuration complexity, and so I started with it. The first Python script became pretty fast a Python module, and in order to test all that I did a quick shell based on the cmd2 module. Which is quite cool by the way.

The secret sauce here is the API of Cisco Nexus switches (aka NXAPI), and how to leverage it with Python. For this last point I used the Python library here:┬á[https://github.com/datacenter/nexus9000/tree/master/nx-os/python/remote\_client](https://github.com/datacenter/nexus9000/tree/master/nx-os/python/remote_client), or from here, with a .tgz file you just need to unpack:┬á[https://developer.cisco.com/fileMedia/download/0eb10f7e-b1ee-432a-9bef-680cb3ced417](https://developer.cisco.com/fileMedia/download/0eb10f7e-b1ee-432a-9bef-680cb3ced417). In my test I have Nexus 9000, I understand the Python functions would work slightly differently with other Nexus, testing N7Ks or N5Ks would be a logical next step here.

So at the end a shell with commands to create networks and tenants across multiple switches emerged. And equally important, to remove the config when it is not required any more. I recorded a demo in this video, so that you can see the shell in action without having to install it:

<figure class="tmblr-embed tmblr-full">[youtube https://www.youtube.com/watch?v=9T1qs-lBGqg?feature=oembed&amp;enablejsapi=1&amp;origin=https://safe.txmblr.com&amp;wmode=opaque&amp;w=540&amp;h=404]</figure>

Another Cisco colleague told me once that he doesnÔÇÖt read any blog that doesnÔÇÖt include code, so here you are: I have uploaded all functions in a single module in Github:┬á[https://github.com/erjosito/evpn\_shell](https://github.com/erjosito/evpn_shell).

WARNING: I wouldnÔÇÖt recommend anybody to use this thing in a production network, this is more of a concept validation. A lot of stuff should be improved before calling this shell┬áÔÇ£production-readyÔÇØ (for example, encrypting the passwords). But this gives you an idea of what is possible with scripting and Cisco Nexus REST APIs.

