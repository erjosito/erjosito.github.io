---
layout: post
title: Firewall insertion with ACI
date: 2015-12-18 21:14:03.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- ciscoaci
tags: []
meta:
  tumblr_erjosito_permalink: http://erjosito.tumblr.com/post/135463071022/firewall-insertion-with-aci
  tumblr_erjosito_id: '135463071022'
  _wp_old_slug: '135463071022'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2015/12/18/firewall-insertion-with-aci/"
---
Hey there,

So this is probably my last post of the year, it has been a fun ride! So far I have been able to keep the goal┬áÔÇ£no post without codeÔÇØ (excluding the news updates), letÔÇÖs see if I can keep it up next year.

But letÔÇÖs turn to our matter of the day. Have you ever configured L4-L7 Service Insertion with service graphs in ACI? It is all about doing two tasks, where one of them is optional:

1. Bring a L4-L7 device (like a firewall or a load balancer) into the data path
2. Put some configuration on that L4-L7 device (optional)

By the way, step 2 has become optional since the latest ACI release 1.2 with what we call ÔÇ£unmanaged service graphsÔÇØ (you do the L4-L7 device configuration outside APIC).

Obviously, you still can insert firewalls and load balancers the same way we always have, using VLAN/subnet/VRF stitching. So why going over the hassle of doing this service insertion thing, specially when it is not the easiest thing to configure in ACI?

First, as usual, letÔÇÖs define some focus for our question. I will concentrate on the Cisco ASA. Other firewalls are supported for ACI service insertion as well (Palo Alto, Fortinet, Checkpoint), but the ASAÔÇÖs device package has the best capabilities.

I decided to test a cluster of physical firewalls in routed mode, and got the help of two colleagues, since I am not that expert in firewalling: Stefan D├╝rnberger and Goran Saradzic. BTW, they will be presenting in CiscoLive, do not miss their sessions on DC Security if you happen to be in Berlin next February.

So What were our findings? I have created a series of videos (so far 3, more might come) with the main benefits (in my opinion)

## One API for the whole network, quick insertion/removal, self-service

Having the possibility of orchestrate from a single place the full L2-L7 network. It is not only having a REST API, but the helper tools as well that make that API usable (as opposed of just having a 400-page API reference guide). Think API inspector, Python/Ruby SDKs, Arya, Save As / Post GUI operations, etc.

For example, you could easily build a self-service portal with which customers can decide whether using the embedded ACI security (up to L4), or including some additional L4-L7 security coming from the ASA (and SourceFire).

You can see more details here (7:55):

<figure class="tmblr-embed tmblr-full">[youtube https://www.youtube.com/watch?v=0kvpKNudt0k?feature=oembed&amp;enablejsapi=1&amp;origin=https://safe.txmblr.com&amp;wmode=opaque&amp;w=540&amp;h=405]</figure>
## Easier troubleshooting

The next question was: if the network is aware of the services inserted, it should provide some context in troubleshooting? This is why we explored what the Troubleshooting Wizard looks like, when there are firewalls inserted over Service Graphs.

As you can see here, ACI can give relevant information (such as counters from the source to the firewall, and from the firewall to the destination).

I used the chance as well to try the relatively new ÔÇ£SPAN to APICÔÇØ function. I would have killed for having that one in my days as network admin, for not having to travel to the DC every time I needed to capture traffic.

<figure class="tmblr-embed tmblr-full">[youtube https://www.youtube.com/watch?v=l4fbclJfVkw?feature=oembed&amp;enablejsapi=1&amp;origin=https://safe.txmblr.com&amp;wmode=opaque&amp;w=540&amp;h=405]</figure>
## Ruleset management

And finally, how do we address the constant pain of too complex firewall rulesets? Multiple answers on that one:

- Since ACI is like a distributed L4 packet filter, you can configure┬áÔÇ£permit tcp any any eq xxxÔÇØ kind of rules in your firewall that take care about TCP normalization or application-specific firewalling. ACI will do the rest, and you donÔÇÖt need to modify the ruleset every time servers come and go
- But wait, there is more. Some customers like to have in their firewalls the full policy, no┬áÔÇ£any to anyÔÇØ. You can use object groups that ACI will update automatically. How cool is that? You have IP address information which is always up to date, but you donÔÇÖt need to maintain it.
- And lastly, what about the list of protocols to be allowed? APIs come to the rescue here as well. Why not┬áÔÇ£offloadingÔÇØ that task to our own customers, giving them a self-service portal to configure them? This is what we did in this demonstration. Again, the enabler is not only ACIÔÇÖs REST API, but all the helper tools around it (and more specifically, the API inspector and Save As functions).

Have a look (6:46):

<figure class="tmblr-embed tmblr-full">[youtube https://www.youtube.com/watch?v=7JNQE6aCYos?feature=oembed&amp;enablejsapi=1&amp;origin=https://safe.txmblr.com&amp;wmode=opaque&amp;w=540&amp;h=405]</figure>

And as usual, here all the code that I have used for these demos, plus the tenant configuration, plus the firewall configs:┬á[https://github.com/erjosito/sirius](https://github.com/erjosito/sirius)

What do you think about service insertion in ACI? Are you happy with VLAN/subnet/VRF stitching? Would you rather go for managed or unmanaged service insertion with ACI? Were these demos helpful? Have you already watched The Force Awakens? (awesome!)

