---
layout: post
title: If you like Cisco UCS or ACI, you are going to love Azure (Stack)
date: 2017-01-22 11:51:54.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- azure
- ciscoaci
tags: []
meta:
  _edit_last: '107159671'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/823121010278928384";}}
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '1026581646'
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2017/01/22/if-you-like-cisco-ucs-or-aci-you-are-going-to-love-azure-stack/"
---
Do you remember when Cisco[introduced UCS](http://www.pcworld.com/article/161293/cisco_enters_server_market.html) servers in 2009? One of the attributes of Cisco UCS that contributed to its huge success are service profiles:┬áthe possibility of describing the configuration and personlity of a server in a declarative language, and deploying it in multiple pieces of hardware.

After a while, Cisco extended that concept to the network with [Cisco ACI](http://www.cisco.com/go/aci): now you could describe your network properties in a declarative language, independently of the amount of devices in your fabric, and deploy that configuration over a REST API. I still think that this is one of the things that make Cisco ACI the best data center network on the planet.

But customers kept asking for more: you have made it for servers, you have made it for the network, when will you do it for the whole data center? This is a daunting endeavor, that no hardware vendor has yet accomplished, to the best of my knowledge. However, this is something that already exists in the public cloud, and it is called "Azure Quickstart Templates".

Behind this relatively cryptic name you can find a similar concept to the one of Cisco UCS service profiles or Cisco ACI application network profiles, but for the whole data center. Yes, you read right: you can describe in a JSON-based declarative language what your VMs are going to look like, what your network is going to look like, your storage, your load balancers, your web application firewall, your VPN connections, your Web applications, your Linux container service, everything! And you can reuse those "templates" once and again and again.

You can find many great examples of those templates in Github┬á[here](https://github.com/Azure/azure-quickstart-templates), ready for you to use them for your own purpose. The possibilities are limitless:

- This is really┬á **infrastructure as code** : you can document in a template the infrastructure on which an application was tested. If you deploy that template in production, you can be sure there will be no nasty surprises (at least from this perspective).
- **Standardization** : as you probably know, there are many options when deploying objects in Azure. Even for a simple VM there are tens of choices you make at deployment time. As organization, you probably have standards, and you may want to document those standards in templates. For example, so that nobody forgets to deploy VMs in a secure way, so that your infrastructure always stays compliant.
- **Simplified automation** : obviously, GUIs are good for people, but you do not want to deploy production stuff through a GUI, but through an API. Deploying templates makes your automation much simple, since instead of automating the deployment of VMs, storage, network, etc. you just automate the deployment of a template. One REST call to rule them all.
- **Business Continuity** : what happens if you need to reset your infrastructure to a previous state? You can┬ábackup your complete infrastructure, and redeploy it in a matter of minutes to the same or a different location.
- **Universal configuration** : forget about disconnected configuration files: one for your load balancer, another one for your switches, etc. A single language to configure everything in your environment.

And these are just a few examples, you are probably thinking of many others. But please allow me to jump┬áto some of the questions that might be floating in your head:

- This good for the public cloud, what about **private and hybrid**? Well, as you might have heard, Azure Stack for private clouds is coming soon, and this is one of the features that it is likely to support, since Azure Stack is going to be following the same object model as Azure.
- How **difficult** is it to describe an existing setup in Azure with a Quickstart Template? Easy peasy, you just go to the resource group where you have deployed your stuff, and you will find a section (called "Automation script") where you can download both the Quickstart Template and some examples to deploy the template using the Azure CLI, .NET, Ruby and Powershell.
- How **flexible** are the template to redeploy them for different environments? Very flexible, you have parameters and variables that can change across deployments. You can build logic such as loops (with the count function) or conditional deployments (using dictionary variables), you have some logic to concatenate variables, generate unique identifiers, etc.

My advice: start playing with Quickstart Templates today, because they are the future of data center infrastructure configuration! Do you agree?

