---
layout: post
title: Migrating your ARM templates to Azure Availability Zones
date: 2017-10-27 14:07:05.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- arm
- azure
- compute
tags:
- arm template
- automation
- availability zones
- azure automation
- azure networking
- high availability
- infrastructure as code
- load balancer
meta:
  _thumbnail_id: '1459'
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '10792620996'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/923898853371383808";}}
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2017/10/27/migrating-your-arm-templates-to-azure-availability-zones/"
---
I have been updating my ARM templates to create VMs and VM Scale Sets (VMSS) to support the new Availability Zones. I have learnt some important concepts that were not obvious for me along the way, and I would like to share them. Before going forward, you can see a template where you can deploy VMs with different options (single VMs or VMSSs, with or without AZs, with or without NSGs, with or without LB, etc) in [this Github repository](https://github.com/erjosito/IaC-Test).

## What did not change

You deploy Vnets and Subnets exactly the same as without AZs. The reason is because Vnets and┬á Subnets are regional resources (not zonal): when you define a Vnet, it is present in all the Availability Zones in a region.

Something that did not change either is the Network Interface object (aka NIC or vnic). When you create a NIC you do not need to specify in which AZ you want to deploy it.

Network Security Groups are also a regional resource: you don't need to specify in which AZ you want to create an NSG, and can reference it from any AZ.

## What changed: public IP addresses

One exception to the NIC regionality is its Public IP Address: "standard" public addresses have been now introduced (the "normal" public IP addresses we knew and loved are now called "basic"). Basic public IP addresses can be assigned to an AZ (they are zonal resources), and Standard public IP addresses can be assigned to a Region (they are regional resources).

At first sight, if you want to have a public IP in your VM, you would think of a basic public IP, right? Well, that is in essence true. But if you ever plan to do cross-AZ external load balancing to that VM, you would have an issue.

The problem is that you would assign a standard public IP to the external load balancer, and Azure today does not support mixing standard and basic public IPs to a single VM (even if the basic public IP is directly associated to the NIC, and the standard public IP indirectly over a load balancer). It makes kind of sense, because if you have an external load balancer you typically want to configure external access to the VMs over Inbound NAT rules.

Another interesting effect is that with regional standard public IP addresses I had to use Static allocation, since dynamic allocation was not allowed for basic public IPs.

Adding up, here the attributes that you would add to a public IP address resource to make it standard and static:

```
"sku": { "name": "standard" }, "properties": { "publicIPAllocationMethod": "static", "idleTimeoutInMinutes": 4 },
```

## What changed: Load Balancer

Similar to the public IP addresses, you have now the Standard version of the Azure Load Balancer, that makes it a regional resource. The Basic version is a zonal resource. So if you plan to load balance across AZs, make sure to deploy standard load balancers associated to standard public IP addressses.

In order to deploy a standard Load Balancer, you just need to add the root attribute "sku" to the load balancer resource in your template:

```
"sku": { "name": "[parameters('lbSku')]" },
```

## What changed: Availability Sets

You are probably used to deploy your VMs in Availability Sets, for a good reason. So it is only normal that even if you deploy your VM across two AZs, inside of each AZ you would want to have your VMs in Availability Sets, to decrease the likelihood of a full AZ going down (in case all your VMs in the AZ are deployed in the same physical host or rack, for example).

Nice try, but at the time of this writing Availability Sets and Availability Zones are two mutually exclusive features. The idea is that Availability Zones are a kind of "unmanaged" Availability Sets, so you can use one or the other, but not both.

&nbsp;

## What changed: Virtual Machines and Virtual Machine Scale Sets (and Managed Disks)

Obviously, the last piece is defining in which AZ you want to deploy your┬á VMs and VMSSs. There is a new ARM property of VMs and VMSSs originally called "zones". You can use the copy attribute of an ARM resource to place VMs or VMSSs on a certain AZ, for example like this:

```
"zones": [┬á ┬á"[string(add(mod(copyIndex(0), variables('maxZones')), 1))]" ]
```

The variable 'maxZones' is the maximum number of zones that you want to use. For example, assuming maxZones=2, With this formula, the first VM will be placed in AZ 1, since 0\2+1 = 1. The second one in AZ 2, the third one again in AZ 1, and so forth.

A corollary of the previous paragraphs is that VMSSs are zonal objects, the same as VMs. You cannot have a VMSS stretching two zones, but you would create two VMSSs, one in each AZ, each with half the total capacity of your planned compute power.

Feel free to try the ARM template in [the Github repo](https://github.com/erjosito/IaC-Test)with these commands (two options provided, with VMs and with VMSSs, play with the other parameters for more options). Note that you can use these commands in Linux, for Windows you would likely have to do some formatting changes (such as the line breaks).

```
az group deployment create -g azlab --name azLabDeployment --template-uri https://raw.githubusercontent.com/erjosito/Iac-Test/master/IaCLab\_master.json --parameters '{ \ "adminUsername":{"value":"lab-user"}, \ "adminPassword":{"value":"yourSuperSecretPassw0rd"}, \ "vmType":{"value":" **ubuntu**"}, \ "vmSize":{"value":"Standard\_D1\_v2"}, \ ┬á"vmCount":{"value": 2}, \ ┬á"azYesNo":{"value":"yes"}, \ ┬á┬á"pipYesNo":{"value":"no"}, \ ┬á┬á"deployLBYesNo":{"value":"yes"}, \ ┬á┬á"lbType":{"value":"external"}, \ "lbSku":{"value":"standard"}}'
```

&nbsp;

```
az group deployment create -g azlab --name azLabDeployment --template-uri https://raw.githubusercontent.com/erjosito/Iac-Test/master/IaCLab\_master.json --parameters '{ \ "adminUsername":{"value":"lab-user"}, \ "adminPassword":{"value":"yourSuperSecretPassw0rd"}, \ "vmType":{"value":" **ubuntuScaleSet**"}, \ "vmSize":{"value":"Standard\_D1\_v2"}, \ ┬á"CapacityMin":{"value": 1}, \ ┬á┬á"CapacityDef":{"value": 2}, \ ┬á"CapacityMax":{"value": 4}, \ ┬á"azYesNo":{"value":"yes"}, \ ┬á┬á"pipYesNo":{"value":"no"}, \ ┬á┬á"deployLBYesNo":{"value":"yes"}, \ ┬á┬á"lbType":{"value":"external"}, \ "lbSku":{"value":"standard"}}'
```

These templates will deploy VMs with a Web site on them that will show you information about the VM the load balancer takes you to.

What is your opinion about Availability Zones? Would be happy to read your comments!

