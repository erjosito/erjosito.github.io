---
layout: post
title: |-
  MAC authentication in Cisco ACI
  (or how to get the features you need)
date: 2015-06-03 21:01:55.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- ciscoaci
tags: []
meta:
  tumblr_erjosito_permalink: http://erjosito.tumblr.com/post/120637625387/mac-authentication-in-cisco-aci-or-how-to-get-the
  tumblr_erjosito_id: '120637625387'
  _wp_old_slug: '120637625387'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2015/06/03/mac-authentication-in-cisco-aci-or-how-to-get-the/"
---
You might be thinking the same that I did: why the heck do you need MAC authentication in a data center? However, I came across this requirement from a customer recently. For this customer this was really important.

Cisco ACI doesnÔÇÖt offer any form of MAC authentication today. Furthermore, unfortunately this requirement is not very common in data centers, so what are the chances that Cisco will implement it? How to solve this problem?

Luckily, ACI is not like any other network: it is highly programmable. So how difficult programming this feature would be? I thought about the ACI Toolkit, a bunch of apps that use a simplified version of ACIÔÇÖs object model. More specifically, I thought about the End-Point-Tracker application, a script that monitors all servers that connect and disconnect to/from the network, and inserts these movements in a database, so that you can track what your end hosts are doing. BTW, you can find it here: ┬á[https://github.com/datacenter/acitoolkit/tree/master/applications/endpointtracker](https://github.com/datacenter/acitoolkit/tree/master/applications/endpointtracker).

So would it be possible modify this End Point Tracker so that instead of recording End Point movements in a database, it compares the MAC address of the End Point trying to connect to the network, and either allow or disallow that connection?

Of course! Even with my poor programming skills (what is obvious in the code), I could do that in a couple of nights. You can find the script in my GitHub repository:┬á[https://github.com/erjosito/aci\_mac\_auth](https://github.com/erjosito/aci_mac_auth). You can see what some lines of code can do: you can write the functionality you need into ACI yourself, without having to wait for Cisco to roll that feature that apparently you are the only one in the world in need of!

I recorded a quick demo, so that you can see the script in action here:

<figure class="tmblr-embed tmblr-full">[youtube https://www.youtube.com/watch?v=Brq_lKd-RT8?feature=oembed&amp;w=540&amp;h=404]</figure>

What other ideas do you have to code on top of ACI?

