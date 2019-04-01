---
layout: post
title: Network dashboards
date: 2015-12-09 22:26:20.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- ciscoaci
tags: []
meta:
  tumblr_erjosito_permalink: http://erjosito.tumblr.com/post/134880086272/network-dashboards
  tumblr_erjosito_id: '134880086272'
  _wp_old_slug: '134880086272'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2015/12/09/network-dashboards/"
---
After a long time without blogging, two entries in one day. I wanted to share some ideas about network dashboards and Cisco ACI. If you are managing a┬áÔÇ£traditional networkÔÇØ (that is, everything that is not called ACI :-), you are probably familiar with non-network folks rolling their eyes when they see you typing CLI commands at light speed. Eventually some manager will ask something like┬áÔÇ£would you be able to tell me how our network is doing, so that even I understand it?ÔÇØ

So we come to the concept of dashboards. One option is buying some software that has this kind of functionality. But hey, you already bought a network, why should you be buying stuff to operate that network?

That is the idea behind Cisco ACI. It already comes with some dashboard. For example, this is what you see when you log into the ACI Advanced GUI (note that since the last version there is a simplified GUI too):

![tumblr_inline_nz42uqxPG51tnwog6_1280]({{ site.baseurl }}/assets/images/tumblr_inline_nz42uqxpg51tnwog6_1280.png)

You can see some interesting stuff, like the overall health of the network, both now and in a time-based graph, an alert summary, and what surprises most people, the tenants affected by those alerts. Traditional networks are not aware of the tenants or the applications using them, this is one of the reasons for the┬áÔÇ£AÔÇØ in ACI.

But wait, there is more. What if you are not interested in the whole network, but in \*your\* network? How is the network for my application doing? Good news: there are other dashboards, for example this one tenant-focused, showing the health of the applications that belong to that tenant:

![tumblr_inline_nz430yQD2w1tnwog6_1280]({{ site.baseurl }}/assets/images/tumblr_inline_nz430yqd2w1tnwog6_1280.png)

But you cannot create a GUI that everybody likes. I am sure some of you are thinking:┬áÔÇ£doesnÔÇÖt look too good, I would have done it differentlyÔÇØ. You know what? Do it!

This is what some folks like my admired colleague Paul Lesiak did: using the Ruby SDK for ACI he created a widget-based dashboard ([https://github.com/datacenter/acirb](https://github.com/datacenter/acirb)) that can be installed on a Web server that has connectivity to the APIC. This is what it looks like running on my Mac:

![tumblr_inline_nz436eBN0x1tnwog6_1280]({{ site.baseurl }}/assets/images/tumblr_inline_nz436ebn0x1tnwog6_1280.png)

But you know what? Everybody is different. A customer recently asked me whether something in PHP could be done. They donÔÇÖt have a lot of Ruby expertise (yet), and they wanted something really simple: some kind of dials that showed how many end points were registered with the EPGs of an app. No problem! I created for them a simple PHP-based dashboard ([https://github.com/erjosito/stuff/blob/master/acme\_summary.php](https://github.com/erjosito/stuff/blob/master/acme_summary.php)):

![tumblr_inline_nz43cmWdMM1tnwog6_1280]({{ site.baseurl }}/assets/images/tumblr_inline_nz43cmwdmm1tnwog6_1280.png)

By now you see where I am going. A colleague recently asked me whether a Mac OS X Desktop Widget for ACI would make sense. I found the idea intriguing, and I did a first implementation ([https://github.com/erjosito/stuff/tree/master/ACIHealth.wdgt](https://github.com/erjosito/stuff/tree/master/ACIHealth.wdgt), please take into account it still needs a good deal of tweaks):

![tumblr_inline_nz43g65JoY1tnwog6_1280]({{ site.baseurl }}/assets/images/tumblr_inline_nz43g65joy1tnwog6_1280.png)

You can see it along other Desktop widgets. It shows the overall network health (95%, as you saw in the very first screenshot in this post), and there are three main panels: one with tenant health (shown in the picture), the second one with switch health, and the third one with a list of the aggregated faults in the network.

IsnÔÇÖt that cool? Tell me a network were developing this kind of integrations is that easy! BTW, if you want to know more about this stuff, donÔÇÖt miss my session at the next Cisco Live in Berlin!

One last question: what ideas do you have for a cool network dashboard?

