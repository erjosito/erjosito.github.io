---
layout: post
title: Configuring your network from Excel
date: 2015-09-26 00:02:35.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- ciscoaci
tags: []
meta:
  tumblr_erjosito_permalink: http://erjosito.tumblr.com/post/129878491127/configuring-your-network-from-excel
  tumblr_erjosito_id: '129878491127'
  _wp_old_slug: '129878491127'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2015/09/26/configuring-your-network-from-excel/"
---
What is the best way to configure a network? There are probably a lot of different opinions out there. Some will say there is nothing like CLI. Others will prefer GUIs. And in this last point people will not agree on what is the best GUI: this one is too complex, this one has too few functions, that one is too slowÔÇª

So let me tell you my opinion. There is not such a thing as┬áÔÇ£theÔÇØ best way to configure a network, but I bet we could try to find ÔÇ£yourÔÇØ best way to configure┬áÔÇ£yourÔÇØ network.

I would start looking at your processes. When you get network requirements, which form do they have? WouldnÔÇÖt it be nice being able to put the requirements into the network, in the same form that you get them?

And that is exactly what you can do with API-based networks. More specifically, with ACI we have tried to build a good GUI, with some wizards to make it easier. Additionally we are going to bring a simplified version of the GUI and a CLI in the next release (see in Youtube for previews of the [simplified GUI](https://www.youtube.com/watch?v=8v4Ma_OxmPg) and the [CLI](https://www.youtube.com/watch?v=94DNafR0ABE)).

But the best thing we have in ACI (in my opinion) is its API. For example, imagine you have a cable plan in an Excel spreadsheet. Why bothering in opening up the APICÔÇÖs GUI? Why not letting Excel configuring ACI directly?

This is what I have tried to show here:

<figure class="tmblr-embed tmblr-full">[youtube https://www.youtube.com/watch?v=LcOowZJyPwo?feature=oembed&amp;enablejsapi=1&amp;origin=https://safe.txmblr.com&amp;wmode=opaque&amp;w=540&amp;h=405]</figure>

Calling up a REST API from Excel VBA running on a Mac is not as easy as Python, but I managed to find an [HTTP client](https://github.com/VBA-tools/VBA-Web) and a [JSON parser](https://github.com/VBA-tools/VBA-JSON).

So after you have documented the way you want your servers connected, you press a button and your network gets configured. And if you donÔÇÖt like that, you have an┬áÔÇ£deprovisionÔÇØ button to remove everything.

This is just an example, in my opinion the future of infrastructure configuration will not be using the CLI or the GUI that your network vendor gives you, but getting your own tools to speak to the software and hardware in your DC.

BTW, in case you are interested, I put the spreadsheet here:┬á[https://github.com/erjosito/stuff/blob/master/VBA-Cable\_Plan.xlsm](https://github.com/erjosito/stuff/blob/master/VBA-Cable_Plan.xlsm)

What do you think about this? What is your favourite network config tool?

