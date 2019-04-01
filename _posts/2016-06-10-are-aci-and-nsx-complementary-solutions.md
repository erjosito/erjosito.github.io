---
layout: post
title: Are ACI and NSX complementary solutions?
date: 2016-06-10 07:26:00.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories: []
tags:
- cisco
- ciscoaci
- microsegmentation
- network
- nexus9000
- nsx
- sdn
- vmware
- vmwarensx
meta:
  tumblr_erjosito_permalink: http://erjosito.tumblr.com/post/145698499432/are-aci-and-nsx-complementary-solutions
  tumblr_erjosito_id: '145698499432'
  _wp_old_slug: '145698499432'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2016/06/10/are-aci-and-nsx-complementary-solutions/"
---
Lately there seems to be a lot of buzz in the press about combining Cisco ACI and VMware NSX to achieve a comprehensive SDN solution. Nothing bad with that, other than it creates some times the perception that NSX and ACI need themselves mutually. And hence this post, since I disagree with this perspective.

First of all, the usual disclaimer: I happen to work for Cisco, but I will try to stay objective in this post. Please tell me if you donÔÇÖt think I achieved that.

I like to compare technology to everyday situations, so that it is easier to understand. I will use the analogy of cars and trailers in this post.

Imagine you have an old network, that is imposing a lot of limitations in the way you operate your data center. You could compare it to an old car. And you are trying to solve the following problem: you need to transport big objects. What do you do? You buy a trailer.

![tumblr_inline_o8jns5PJO31tnwog6_1280]({{ site.baseurl }}/assets/images/tumblr_inline_o8jns5pjo31tnwog6_1280.jpg)

The trailer is VMware NSX. It is an add-on to your old network, that solves your problem. Now you can transport big objects. LetÔÇÖs go back to the real world: what problems does NSX try to solve? After speaking to some customers, and reading documentation, these seem to be the most frequent issues that people try to address with NSX:

- Higher security, through microsegmentation and firewall integration
- Automation through a single API
- Automation through integration in vRealize Automation
- Ability of deploy network configurations from vCenter
- Interconnection of clusters separated by IP networks

But wait a minute, are you sure that your old Trabant has enough horse powers to pull the fully loaded trailer? The same way that NSX needs a performant network (according to the design guide, a 10G/40G spine/leaf architecture is highly recommended), you might need some more power there. The performance of your overlay network will only be as good as the performance of your underlay.

So chances are that you need to get a new car to successfully solve your problem anyway. What if you bought the best car/network on the planet today? If you went for Cisco ACI, you would be in a similar situation:

![tumblr_inline_o8jnz0yxiE1tnwog6_1280]({{ site.baseurl }}/assets/images/tumblr_inline_o8jnz0yxie1tnwog6_1280.jpg)

Now we are talking, this looks much better. But wait a second, your new pick-up (your new network, ACI) is now able to transport a lot of stuff! Do you still need the trailer? LetÔÇÖs see what our new car is able to do on its own:

- Microsegmentation? Check
- Integration with next-gen firewalls? Check
- Centralized API? Check
- Configurable from vCenter? Check
- Integrated with vRA? Check
- Multiple locations interconnected by IP? Check

And the previous goals are solved not only for the VMs in a specific hypervisor, but for multiple ones (Hyper-V, Openstack, vSphere), for bare-metal servers and Linux containers (see [https://contiv.io](https://contiv.io)).

But you will tell me: ah, but I had to spent a lot of money in the new car! What if I told you that Cisco ACI does not cost more than traditional networks? In other words:

![tumblr_inline_o8jo80MQI91tnwog6_1280]({{ site.baseurl }}/assets/images/tumblr_inline_o8jo80mqi91tnwog6_1280.jpg)

Now it is a no-brainer. You needed a new car anyway. Get ACI, which is the best value for your bucks, and it can cover all of your requirements. If you look really hard, I am sure you can find some object that does not fit in your pick-up (ACI), and maybe you can even convince yourself that that justifies buying the trailer (NSX). Be my guest, you can always attach the trailer to the pick-up. But one thing remains true: the trailer will not be able to drive without a car pulling from it.

