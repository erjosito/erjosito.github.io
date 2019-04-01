---
layout: post
title: Using Ansible to Create and Manage Linux Workloads in Azure
date: 2016-11-25 16:45:58.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- azure
tags: []
meta:
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/802176512183173120";}}
  _publicize_done_14974324: '1'
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '29297587319'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2016/11/25/using-ansible-to-create-and-manage-linux-workloads-in-azure/"
---
This is an exciting post, since it is the first one I write since I joined the Microsoft team. One of the things that have surprised me is the amount of open source solutions that you can deploy effortlessly in Azure. Services ranging from a simple RHEL or Ubunto machine, to complete open source solutions such as the popular platform [Magento](https://magento.com/). You just need to look for them in the Azure Market Place, and chances are that you will find what you are looking for. For example, if you would like to start playing with Jenkins, you can deploy a Jenkins server in Azure without concerning yourself with the installation of the Linux operative system or the application itself.

There are two questions that might be popping in your head. First, what if you don't find what you are looking for in the Market Place? And secondly: what if you need to modify the predefined service that you find in the Market Place, for example by adding additional components? A possible answer to both questions are Configuration Management Tools. If you want to install an application that you have developed yourself (and therefore it is not to be found in the Azure Market Place), you could deploy a simple Linux VM in Azure and install the application using tools like Ansible, Chef or Puppet. Or you could take a predefined service and modify it so that it exactly fit your needs. This is what I am going to describe in this post.

Before we proceed, if you don't have time to read the full thing, you might want to have a look at a short video showing a quick demo on this topic:

https://youtu.be/IT7zlNW9mCo

By the way, you can find the scripts and playbooks used in the video in my Github repository:┬á[https://github.com/erjosito/Azure-Ansible-Examples](https://github.com/erjosito/Azure-Ansible-Examples).

In the next sections I will briefly describe the required steps in order to manage Azure with Ansible.

## Installing the prerequisites

First things first, you need a system where you are going to control your deployments from. In my case it is a CentOS machine (hosted in Azure of course ;-). You need to install a couple of things there:

1. Python (although chances are that your Linux version already has Python preinstalled)
2. Ansible: you can use installers like apt-get or yum to install the binaries, but you probably want to clone the Github repository too
3. Azure SDK for Python: as documented in the Ansible pages for Azure ([https://docs.ansible.com/ansible/guide\_azure.html](https://docs.ansible.com/ansible/guide_azure.html)), you can use this command:
```
pip install "azure==2.0.0rc5"
```

Chances are that you need to install a few dependencies, or at least that was my case (such as python-devel and openssl-devel, for example).

After you do that, you need to work in the authentication. With Azure it is not as simple as sending a username and a password, since security is paramount for the Azure engineers, and therefore more secure mechanisms have been implemented. You can read about authentication in the Ansible page for Azure too.

## Accessing all your VMs in Azure

You probably do not want to maintain a static list of all your VMs in Azure, but you may want to deploy a certain Ansible playbook to all of them. This is where Ansible's [Dynamic Inventory](http://docs.ansible.com/ansible/intro_dynamic_inventory.html) comes to the rescue. In a nutshell, first you ask Azure to give you informations about all VMs running, and then you execute a task on all of them, or just a subset.

You find the dynamic inventory tool [azure\_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) in the /contrib/inventory subfolder┬áwhere you cloned the Ansible Github repository.

You could quickly test that so far everything is working by just "pinging" all your Azure VMs:

```
ansible -i /home/my\_user\_alias/ansible/contrib/inventory/azure\_rm.py all -m ping
```

Remember that you need to deploy passwordless SSH to your machines witht the public SSH key of the machine where you are running ansible from.

## Deploying a new VM

Now you can use the Ansible Azure extensions to deploy a VM. You can find some examples in the previously mentioned Ansible pages for Azure, or you can refer to the documentation page for the individual modules, for example┬á[https://docs.ansible.com/ansible/azure\_rm\_virtualmachine\_module.html](https://docs.ansible.com/ansible/azure_rm_virtualmachine_module.html) for azure\_rm\_virtualmachine. Kudos go to Ansible for the excellent documentation, where every module has a detailed description of all of the options and even some examples.

In my case, I manually deploy the NICs, mainly because I want to have full control of the objects created. For example, I create Network Security Groups where not only port 22 (SSH) is opened, but port 80 (HTTP) as well.

You can find an example of a playbook used to create a VM [here](https://github.com/erjosito/Azure-Ansible-Examples/blob/master/azure-playbooks/new_vm_web.yml). You can deploy this playbook specifying the localhost as target host, you do not need the azure dynamic directroy here:

```
ansible-playbook ./azure\_playbooks/new\_vm\_web.yml --extra-vars "\<vm\_name\>"
```

Do not forget to replace \<vm\_name\> with an unique name. This is important, because the playbook will try create a global DNS name for \<vm\_name\>.westeurope.cloudapp.azure.com, and this operation will fail if the name already exists. By the way, the playbook will gracefully raise an exception if that name exists, before even trying to deploy the VM.

After the script is done, you could now retry the ansible "ping" of previous sections, and you should see that the new machine is there, waiting to be configured!

## Configuring an existing VM with Ansible

Now we have a VM, what shall we do with it? Possibilities are endless, or rather, they depend on what you are trying to achieve. In this case, we will install a Web server and deploy Web content which was previously stored in Github.

You can use standards playbooks for doing that, you can find my example [here](https://github.com/erjosito/Azure-Ansible-Examples/blob/master/azure-playbooks/httpd.yml). You can deploy this playbook with the following command (note that I am using externally supplied variables to specify the VM where we want to run the installation on):

```
ansible-playbook -i ./azure\_rm.py ./azure\_playbooks/httpd.yml --extra-vars "vmname=testvm04"
```

Here I created a symbolic link of azure\_rm.py, so that I do not need to type the full path.

SSH probably prompted you to accept the new system, since it is the first time that it has been seen (of course, we have deployed it just a few seconds ago), and obviously it is not yet in the local .ssh/known\_hosts file. You can use the environment variable┬áANSIBLE\_HOST\_KEY\_CHECKING to prevent known\_hosts checking, already you should really know what you are doing here.

After you are done with the VM, you can delete it with Ansible the same way you create it. You can find an example of a playbook to delete the VM and other objects associated with it [here](https://github.com/erjosito/Azure-Ansible-Examples/blob/master/delete_vm.sh).

## Conclusion

In this post I tried to give you some hints about where to find the documentation related to Azure and Ansible, and some examples of playbooks and deployment commands to kickstart in the Azure world. Thanks for reading!

&nbsp;

&nbsp;

