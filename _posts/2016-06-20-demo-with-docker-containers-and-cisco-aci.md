---
layout: post
title: 'Demo with Docker containers and Cisco ACI integration: Contiv'
date: 2016-06-20 22:36:33.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- ciscoaci
- docker
tags: []
meta:
  tumblr_erjosito_permalink: http://erjosito.tumblr.com/post/146227205732/demo-with-docker-containers-and-cisco-aci
  tumblr_erjosito_id: '146227205732'
  _wp_old_slug: '146227205732'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2016/06/20/demo-with-docker-containers-and-cisco-aci/"
---
You might remember my old post on Linux container integration with ACI by using the ÔÇ£network=noneÔÇØ option in the old Docker implementation:┬á[http://erjosito.tumblr.com/post/110063660087/cisco-aci-and-linux-containers](http://erjosito.tumblr.com/post/110063660087/cisco-aci-and-linux-containers).

In the meantime, the networking concept in Docker has considerably evolved, including in the latest releases the concept of network driver plugins ([https://docs.docker.com/engine/extend/plugins\_network/](https://docs.docker.com/engine/extend/plugins_network/)). Contiv ([https://contiv.io](https://contiv.io)) is one of those plugins, that enhances the native Docker functionality for both storage and networking, so that intent policy can be specified for infrastructure as well. This open source project is attracting quite a lot of interest, as the recent ONUG award proved ([http://blogs.cisco.com/datacenter/cisco-open-source-project-contiv-winner-at-onug-spring](http://blogs.cisco.com/datacenter/cisco-open-source-project-contiv-winner-at-onug-spring)).

First of all, we will focus in this post in one of the halves of Contiv (networking), while leaving the other one (storage) for a different post.

Contiv offers multiple networking options for Linux containers: either native BGP for scalable IP networks, VXLAN overlay networks, or VLAN-based for better performance (in case you donÔÇÖt have VXLAN offload NICs in your server) or interaction with VLAN-based physical networks.

I get often the question to run customer demos with Contiv integration, so I decided to record one and post it in a video, here you have the result:

<figure class="tmblr-embed tmblr-full">[youtube https://www.youtube.com/watch?v=b7Gb1CiSzJQ?feature=oembed&amp;enablejsapi=1&amp;origin=https://safe.txmblr.com&amp;wmode=opaque&amp;w=540&amp;h=304]</figure>

Essentially, the main goal of the demo is showing that creating and tearing down containers with Contiv is essentially no different to the native docker implementation. That is, the Linux container admin is not burned with additional processes. However, contiv will extract the network-relevant information, and use it to create (and tear down) secure container networks at the same speed. This way operators have the same visibility for Linux containers as for other DC workloads.

Additionally, all the good operational tools of ACI can now be applied for Linux containers as well. Think about the troubleshooting wizard for example. Linux containers are thus promoted to first-class citizens in the data center, from a network perspective.

