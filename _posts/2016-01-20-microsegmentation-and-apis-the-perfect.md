---
layout: post
title: 'Microsegmentation and APIs: the perfect combination'
date: 2016-01-20 13:09:38.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- ciscoaci
tags: []
meta:
  tumblr_erjosito_permalink: http://erjosito.tumblr.com/post/137682901332/microsegmentation-and-apis-the-perfect
  tumblr_erjosito_id: '137682901332'
  _wp_old_slug: '137682901332'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2016/01/20/microsegmentation-and-apis-the-perfect/"
---
You might have heard the hype about microsegmentation in the DC, and multiple definitions of this term. Paraphrasing Juan Lage, it is the possibility of setting up policies with end point granularity. In other words, to be able to take any end point in the DC and change its security policies, without having to alter any of its attributes (like IP address).

Note that I am speaking most of the time about end points. These could be virtual machines, but they could be something else like bare metal servers, Linux containers or storage systems).

This as such is a very powerful capability, that until recently not many network technologies could boast about. Cisco ACI is one of them.

One example: you modify the security policy of some end points when you decide that their role is going to change (for example, when moving an application from staging to production).

Does this mean that network admins have to define as many security policies as end points exist in the DC? Not necessarily. In my opinion, this feature makes most sense when use in an automated fashion.

Another example, but this time no admin takes any action: a security tool detects that a server has been compromised, and moves it to quarantine. This is what I am showing in this video:

<figure class="tmblr-embed tmblr-full">[youtube https://www.youtube.com/watch?v=UmufoFsjXoM?feature=oembed&amp;enablejsapi=1&amp;origin=https://safe.txmblr.com&amp;wmode=opaque&amp;w=540&amp;h=304]</figure>

Here, the┬áÔÇ£security toolÔÇØ detecting that a server is compromised is an ASA firewall with NGIPS, that detects suspicious activity coming from a compromised end point. This could be your favourite virus scanner detecting malicious code, or OpenDNS detecting that some server is sending data where it should not.

There are multiple ways to tell the network to move the compromised server somewhere else. In this example the NGIPS is setting a custom attribute in vSphere leveraging dynamic EPG (End Point Group) assignment in Cisco ACI (also known as Microsegmentation EPGs). After setting a flag in the VM (in this case an attribute called┬áÔÇ£AttackedÔÇØ is set to┬áÔÇ£TrueÔÇØ), Cisco ACI will recognise it and move the end point to another EPG with a different security policy (typically no access to other DC areas, but with the possibility to investigate and remediate the compromised server).

A more generic way to modify the security policy would be directly interacting with ACI and using IP-based EPGs, since this would not depend on the hypervisor or the workload form factor. I have this in my To-Do list.

What do you think? Do you need this functionality in your DC?

