---
layout: post
title: 'A Recipe for DevOps: APIs + Collaboration + Analytics'
date: 2016-08-16 23:32:01.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- ciscoaci
tags: []
meta:
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '25856131554'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/765677525548736512";}}
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2016/08/16/a-recipe-for-devops-apis-collaboration-analytics/"
---
Imagine you are an IT admin. Your cellular rings while you are drinking your ritual cup of tea in the cafeteria: "why don't you answer on our Instant Messenger? We have a serious problem in our production environment". So much for your tea break. Anyhow, back to your desktop, and on to the Incident Management tool. As with any other severe outage, lots of people writing their perspective of the problem, so the first task is getting useful and objective information out of the apparently white noise of the distressing, blaming or moaning statements that some individuals like to enrich the Incident Record with. Apparently a temporary network glitch has caused an application to go in split brain mode, breaking havoc for other services that consume that application. You now need to find documentation about how the servers for that applications are connected to each other. Not a daunting task, but while you are looking for the specific details you surprise yourself wishing that the docs are up to date. Maybe finding the network admin responsible for the specific technology that caused the problem would not be a bad idea. And who might know why the heck a simple network glitch triggered all this waterfall of problems? Maybe you should go offline in your Instant Messaging app, since it does not stop to beep with new messages. Now you can start to troubleshoot, but where? You open your terminal application, breath deep, and get ready for yet another intense problem-solving day.

That situation might be familiar to some of you. What is wrong there? If you are thinking that the main problem of that organisation is an unstable network, you are wrong. Infrastructure will fail. Inevitably. What sets organisations apart from each other is the efficiency in which they can cope with infrastructure challenges, being at the provisioning, operating, troubleshooting or decommissioning layers. You may come up with other issues, but these are the ones that are often seen in businesses across the globe:

1. Too many disconnected communication channels. Whether it is your synchronous (instant messaging for example) or asynchronous (email, whatsapp, community discussions or incident record annotations), with or without voice, with or without video, it is a real challenge to consolidate information that comes over these multiple mediums.
2. IT infrastructure is hard to manage. Specially in the network area, where manual interactions with each individual device is the rule rather than the exception. Besides, there is a blatantly lack of APIs (or ability to use them) in certain areas, making information gathering an ardous process.
3. Static documentation: due to the inefficiencies in IT infrastructure management, static documentation is the only way in which relevant information can be gathered. The problem with static documentation is, well, it is static. That means, it is often outdated. Which will leave you alone in the rain.

But there is an easy solution to your problem. As every analyst and industry expert is touting, you should adopt a DevOps mentality, which will solve of the problems above. If you listen to some organisations claiming to have embraced a DevOps culture, they bring everything down to using certain tools: "Oh yeah, we deploy our applications with Ansible, we are totally DevOps guys". However, DevOps is about bringing down the barrier between Dev and Ops (thank you Captain Obvious!). There is obviously a critical tool component to it, but how to automate application deployment is by far not the most difficult problem to solve.

You might ask: "how am I going to bring down the communication barriers between Dev and Ops, if I cannot communicate efficiently inside of my Ops organization?" Great question. One of the first things you should do if it is about bringing people closer, is enabling them to communicate seamlessly. One way to do that is bringing them together in the same room. And that is a way that some businesses have been practicing: through organisational changes you build multi-disciplinar teams with subject matter experts from different areas, and you put all of them together in the same office. Obviously, this cannot be a long-term solution, since you are very dependant on organization barriers and physical constraints ("how many people did you say that you can fit in that room?").

Another way to look at it is through virtual teams, possibly even geographically dispersed. How can you make them feel as if they were in the same room? With an efficient collaboration strategy. I read some time ago a very interesting blog by Vincent Geffray, in two parts: [here](http://devops.com/2016/02/29/embracing-a-devops-culture-part-1/) and [here](http://devops.com/2016/05/10/embracing-devops-culture-part-2/) about this topic, a text that I would encourage you to read. Vincent takes a similar argumentation line, in which he sets out the case that only through an effective communication strategy you can truly bring down the barriers that will enable you to embrace a DevOps mentality. But is there any tool out there that can make out something meaningful out of this collaboration app salad? Actually there is one: with Cisco Spark ([https://www.ciscospark.com/](https://www.ciscospark.com/)) you can come one step closer to enabling efficient communication channels. We have solved problem one, easy as pie. We will see later in this blog how Spark could work in practice in a situation like the one depicted at the beginning of this text.

Let us tackle problem two: infrastructure management. May infrastructure areas have already a very decent level of automation, that enables efficient operations. Virtualization is a prominent example, but physical storage or compute (think [Cisco Unified Compute System](http://www.cisco.com/go/ucs)) have made considerable leaps in the right direction. What is missing? Well, a critical component of absolutely every single IT environment, whether it is based on mainframes, bare metal x86 servers, virtual machines or Linux containers: the network. Why does everybody assume that "it is the network" when there is an issue? Because it is the most pervasive IT infrastructure element today, and one that has kept its obscurity until now. Only highly skilled individuals speaking a cryptic language (thinks like "spanning tree", "broadcast domains" or "routing protocols") are able to type commands at high speed into text windows that will allow them to understand the world.

However, that has changed with [Cisco Application Centric Infrastructure](http://www.cisco.com/go/aci). The same way that you centrally manage your virtual machines from a central GUI or API, or your Cisco UCS server blades from a central GUI or API, now you can centrally manage the data center network from a single GUI or API. You are not impressed? Probably you are right not to be, since this is a natural evolution that other IT areas have taken during the last 15 years, only networking managed to lag behind. Remember all that hype about Software Defined Networking a while back? It was the bursting of the immobility bubble, the masses demanding for a better network, automated, and easier to manage.

Having an API is not the same as using it, though. How many of your products tout REST APIs in their data sheets, but you have never used them? From Cisco we are committed to make our APIs so easy to use as possible. Be it with communities of users that post a myriad of examples where you can get inspired, or with tools that allow to automatically generate API calls when something is done in the GUI, or with scripts that automatically generate Python code, the idea is to bring IT admins without programming experience up to speed as quick as possible. For example, check out the developer site for Cisco ACI ([https://acidev.cisco.com](https://acidev.cisco.com/)) or the one for Cisco Spark ([https://developer.ciscospark.com](https://developer.ciscospark.com/)).

Alright, we have solved problem two. Even the network is easier to manage now. Actually problem three is the easiest to solve, since through APIs documentation could be quickly generated on demand that helps to quickly identify the infrastructure components that are involved in a specific issue. For example, with the Troubleshooting Wizard in Cisco ACI, the network automatically generates a diagram that shows you where the end points having an issue are attached to. Or with scripts that you can use in order to dynamically generate Word documents addressed to different audiences and with different levels of detail, out of network configuration (for example [here](https://github.com/erjosito/stuff/blob/master/json2doc.py)). So fixing problem three might be now easier than we thought.

So let us have a look at what the example depicted at the beginning of this text might look like now:

Imagine you are an IT admin. Your cellular beeps while you are drinking your ritual cup of tea in the cafeteria: a virtual Spark room has been created, since apparently there is a problem in production. Links to the most relevant documentation repositories have been automatically added, as well as some dynamically generated logs and documents containing information specific to this problem. The Spark room was opened automatically by your Cisco ACI network, which saw the health score for one of your critical applications going temporarily down. You notice that the relevant network specialists are present in the virtual room, as well as some developers responsible for the application. Spark has invited all the relevant attendees automatically, since the IT infrastructure is application-centric, that means, infrastructure failures can be mapped to applications automatically.

One developer is saying that there was a recent update to the high availability module of the application, responsible for the heart beats. When she wants to know the latest events that impact the application, a simple Spark command brings that information into the virtual room, without even having to open an additional Web browser. Interesting, a server port went down, but that server should have been dual-homed. The network admin verifies the connectivity for that server in Cisco ACI, that shows that only one Network Interface Card is actually connected. Problem solved.

What are the key learnings here?

- Consistent and effective collaboration across organisation silos, bringing together individuals across multiple geographic locations in virtual rooms
- The API of the application platform allows other systems (like the network in this example) to automatically start conversations and invite all relevant resources.
- Application-centric infrastructure management allows to involve the relevant developer resources in operations activities
- Infrastructure APIs allow to dynamically retrieve information from the infrastructure and make it available in the virtual rooms, or even to interact with it in real time directly from the collaboration platform
- DevOps is about continuous learning: detecting problems as quick as possible and correcting them so that the whole infrastructure is more resilient is of paramount importance

You might ask whether this picture is the ideal world, the end of the journey. Nothing further from the truth! Actually this is the beginning. Specially with the recent launch of a technology that could take this process to the next level, especially in the area of problem detection: Cisco Tetration Analytics. Now there is a systems that sees every single packet traversing the network, and analyses dependencies in real time. A big data platform conceived for DC infrastructure analytics. The wealth of information and actionable items that these analysis can generate are almost endless. I will try to give you some examples of the possibilities that real-time network analytics could offer in the future:

- Network analytics can be used to generate Application Dependency Mappings. Analysing how different servers that belong to the same application are connected and detecting anomalies (like the problematic single-homed server of the example above) are the next logical step. You can detect and correct these misconfigurations before they degenerate into actual problems with application impact.
- Applications communicate over the network. Moreover, application components communicate over the network too. And these components tend to be smaller and smaller, specially for applications developed with a Microservices Architecture. The network becomes the communication bus over which your application modules talk to each other. Network analytics can baseline communication patterns, and raise alerts when something unusual happens. This deviations from the baseline might correspond to a period o unusual activity (like a Black Friday), an application update that changes the way that modules interact, or even a security breech that is taking place. Understanding, detecting and acting on these anomalies is crucial.
- Predictive analytics is the next frontier. Correlating network problems to each other, and being able to predict them is of paramount importance. Think for example of spanning tree loops. Although a problem of the past for customers that have already embraced technologies like Cisco ACI, we can use them as example for predictive analytics. When a spanning tree loop occurs, traffic starts filling up the network. You can see signs of the problem for some time, before the network actually melts down. Being able to act upon those signals is crucial. And although these signals are obvious in spanning tree loops, they are not so simple to see in todays complex infrastructure, where you need sophisticated analytics technologies.

Wrapping up: if you want to break silos in your organization (some call it DevOps), you need (at least) three things:

- Efficient, consolidated collaboration
- An application-centric IT infrastructure
- Network analytics
