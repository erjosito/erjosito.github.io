---
layout: post
title: 'Migrating VMs to the cloud: send or remake?'
date: 2018-04-03 01:20:23.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- arm
- azure
- vmware
tags:
- asr
- azure migrate
- azure site recovery
- cloud migration
- iaas
- infrastructure as a service
- microsoft azure
- public cloud
meta:
  _thumbnail_id: '1642'
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '16401280658'
  timeline_notification: '1522714824'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/980963193689387010";}}
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2018/04/03/migrating-vms-to-the-cloud-send-or-remake/"
---
After a long time of no blog writing (mostly busy with my [book on ACI](http://www.ciscopress.com/store/deploying-aci-the-complete-guide-to-planning-configuring-9781587144745)and learning Machine Learning and AI), the messages of new followers of this blog has pushed me into finding the time to write a new blog. Thanks for that!

A question that I have been dealing with lately is that of the best way of migrating on-premises virtual machines to the cloud, namely to Microsoft Azure. As I see it, there are mainly two ways of migrating compute stuff to Azure, what I call "send" or "remake".

Let me illustrate this with an example: imagine you have a paper plane, and you want to send it to somebody. You have at least two options:

- "Send" it: Put the paper plane inside of an envelope, add stamps and recipient's mailing address, and in a couple of days they will receive it.
- Have your recipient "remake" your paper plane, by sending her instructions on how to make a paper plane like yours out of a paper sheet.

## Sending stuff to Azure

Coming back to reality, what is the "send" way of migrating to the cloud? By "send" I mean sending the bits (all of them) inside of the VMs. There are multiple ways to do that, one of them being Azure Site Recovery (ASR). ASR is an Azure service used to provide Disaster Recovery on-premises to on-premises and on-premises to cloud. And if you think about it, migrations are not too dissimilar from DR scenarios from a technical perspective, you are starting your workloads somewhere else.

I created two videos to quickly illustrate the configuration and functionality of Azure Site Recovery, using VMware vSphere as a source environment. For more details about ASR please refer to the [official ASR documentation page](https://docs.microsoft.com/en-us/azure/site-recovery/), it is pretty exhaustive. But I digress, I was talking about the two main steps required to use ASR. The first one involves installing software on-premises, what in the case of vSphere takes the form of an OVA appliance:

https://youtu.be/NshDGYRSF\_c

And the second and more complex, to define all the migration options in Azure:

https://youtu.be/81HxChb1yyY

## Remaking stuff in Azure

So what is the alternative to sending big loads of bytes to Azure? Well, if you think about it, in some cases it would be much simpler just telling Azure to "deploy a CentOS 7.4 VM with apache and PHP on it", rather than moving terabytes of data over the Internet. That is what I mean with "remake", or "re-create" a virtual machine in the cloud.

You could argue that your workloads are more complicated than a simple web server, and I would answer that no matter how sophisticated your applications are, you should have an automated way of deploying them. And that is regardless any potential migration to any cloud, by the way.

If you either have these automated deployment process in place, or are willing to create them, then you could just deploy a VM with the required operative system, and run your application installation routine on top:

1. To deploy virtual machines in Azure you have multiple options, from Powershell to the Azure CLI, including of course Azure Resource Manager (ARM) Templates. You can find literally hundreds of examples of Azure templates [here](https://azure.microsoft.com/en-us/resources/templates/).
2. After you have a virtual machine with a pristine operating system, you can install your application. Sophisticated folks will use Configuration Management tools such as Puppet, Ansible, Chef or Desired State Configuration. However, if you are not on that track, you can just run a script in your VM (a Custom Script Extension in Azure parlour).

I created a Github repository [here](https://github.com/erjosito/AzureBlackMagic)┬áwith examples of that process. In that repo you can find the following:

- An [ARM template](https://github.com/erjosito/AzureBlackMagic/blob/master/genericLinuxVM-templ.json) to create a Linux VM (both Ubuntu and CentOS supported) and a Custom Script Extension
- That [Linux custom script extension](https://github.com/erjosito/AzureBlackMagic/blob/master/ubuntuConfig.sh) will install software, download content (a web page) and mount the data disks associated to the VM. The script is intelligent enough to mount all the data disks, excluding the OS disk and the ephemeral disk
- Azure CLI code to do the same as the ARM template
- Powershell code to do the same for Windows VMs.
- The [Windows custom script extension](https://github.com/erjosito/AzureBlackMagic/blob/master/windowsConfig.ps1)uses Chocolatey to install PHP
- And of course, no password should ever be hard coded! That is why the examples (ARM template parameters, Powershell and Azure CLI) also include who to use passwords stored in an Azure Key Vault

Hopefully you can reuse this code to automatically create your applications in Azure.

## Data Migration

Ah, that pesky matter of data... You will ask, how do I migrate stateful stuff such as databases? This is a complete separate matter, that I will not try to cover in this post. However I will not resist the temptation of giving you a glimpse of my recommendation: if you are able to re-create your database of choice in the cloud, uploading and importing a database backup is probably your best choice.

You have other alternatives to backups, such as using external tools like [Azure Data Migration Service](https://azure.microsoft.com/en-us/services/database-migration/), that would be a good topic for another post.

## So what is the best option for me?

If you have read other posts in my site, you already know the answer: the dreaded "It depends".

Obviously, the more standard your applications are, the easier they will be to re-create in Azure from scratch. Or if you already have automatic app deployment in place, the choice is clear for remake as well.

But what if you don't? Well, my recommendation would be to start thinking about creating those automatic deployment scripts, at least for the most critical of your applications. However, you might not want to do that for all of them, at least not at once. So for certain applications, sending the bits to Azure (over ASR or any other tool) might be a "good enough" option for the time being, until you get time to work on the automation side of things.

Once you have an unattended installation script for an application, your possibilities are greatly enhanced. Here some examples of the benefits:

- Not only you can migrate to Azure more easily, but to any other cloud.
- You will be able to migrate workloads in minutes, without having to wait for terabytes of data to travel over the wire.
- Your Disaster Recovery policies in the cloud will greatly benefit, since you can now bring up your workloads anywhere in the world.
- Recovering from virtual machine failures is much easier (no matter if on-premises or in the cloud), since you can just delete your VM and re-create it from scratch.

## Assessing migration feasibility and costs

By the way, no matter whether you go for sending or recreating your VMs (or a combination of both), or which tools you do to get the job done: you will need to assess which of your VMs can be migrated to Azure in the first place, as well as provide estimations for the associated costs. Azure Migrate is an Azure service that can help you here, and it is very easy to use. In its first release it only supports analysis of VMware vSphere environments, this support is probably going to grow in the future (for one, Hyper-V support would not be the greatest of surprises).

In an Azure Migrate assessment of a vSphere environment there are three phases, that I have illustrated with short videos:

First, deploy an OVA into your vSphere environment, and configure it from its console (it is actually a Windows VM) to start sending data to Azure:

https://youtu.be/5OcoA382fGc

Second, create an Azure Migrate assessment in the Azure portal:

https://youtu.be/ZS8p5pe1w28

And third and optionally, analyze the network dependencies between your VMs with a deep discovery (you will need to install software in each VM for this):

https://www.youtube.com/watch?v=uso6GWDZ2Y8

## Conclusion

I hope I could give you some ideas with this post or the associated [Github repo](https://github.com/erjosito/AzureBlackMagic), let me know if you have questions, comments, or improvement suggestions. Thanks for reading down to here!

&nbsp;

&nbsp;

&nbsp;

