---
layout: post
title: Using NX-API to deploy full networks
date: 2016-06-22 12:37:34.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories: []
tags:
- automation
- lab
- n9k
- networkautomation
- networking
- networklab
- nexus
- nexus9000
- nxapi
- nxos
- sdn
- testing
meta:
  tumblr_erjosito_permalink: http://erjosito.tumblr.com/post/146304199587/using-nx-api-to-deploy-full-networks
  tumblr_erjosito_id: '146304199587'
  _wp_old_slug: '146304199587'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2016/06/22/using-nx-api-to-deploy-full-networks/"
---
Do you have a network lab to try out new technologies? Then you might have experienced this problem: you finished testing technology A, and you start testing technology B. For that you need to reconfigure your devices, obviously. Now you need to try out something new in technology A, for example a new functionality brought in a recent software release.

You would have to save your existing configs (related to tech B), replay your tech A configs, test whatever you want to test, and then replay back your tech B configs. At least for me, that process sounded scary enough so that I did not want to change my running lab unless I had a very good reason to do so.

That has changed recently, with an app that my esteemed colleague Christian J├ñckel has developed in order to quickly snapshot and deploy configurations across whole networks. You can find his work here (as well as a link to my fork):┬á[https://github.com/chjaecke/nexus\_lab\_deploy](https://github.com/chjaecke/nexus_lab_deploy)

You can see a demo video below, but essentially it can download the config of a group of network devices, and document it in a JSON file, which will represent a snapshot of your lab. You can deploy that snapshot with the tool as well, which will restore those specific configs in each one of the devices.

Here you have a short video that gives a glimpse on how this thing works:

<figure class="tmblr-embed tmblr-full">[youtube https://www.youtube.com/watch?v=hb1JS4r4cs4?feature=oembed&amp;enablejsapi=1&amp;origin=https://safe.txmblr.com&amp;wmode=opaque&amp;w=540&amp;h=405]</figure>

Now I feel confident deleting all my VXLAN EVPN config from my lab, and start testing the exciting Segment Routing functionality that is now available in the Nexus 9000 product line.

Would this be useful for you too?

