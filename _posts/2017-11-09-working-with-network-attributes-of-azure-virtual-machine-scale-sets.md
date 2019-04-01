---
layout: post
title: Working with network attributes of Azure Virtual Machine Scale Sets
date: 2017-11-09 11:06:15.000000000 +01:00
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
- az
- azure cli
- azure networking
- virtual machine scale sets
- vm scale sets
- vmss
- vnet
meta:
  _thumbnail_id: '1625'
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '11251337722'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/928564391422939136";}}
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2017/11/09/working-with-network-attributes-of-azure-virtual-machine-scale-sets/"
---
<p>I have been working this week a bit with Azure VMSS, and I thought I would publish my findings, since I could not find this information in the standard documentation.</p>
<p>First of all, if you do not know what a VMSS is, you can read more <a href="https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview">here</a>. Essentially they are farms of VMs that are scaled in and out depending on their load. As you can imagine, so that Azure can deploy more VMs as they are needed, you need to define in advance the configuration of those VMs.</p>
<p>The first thing you might want to do is to deploy a VMSS in your existing Vnet. Well, at the time of this writing Azure GUI does not allow to specify a custom Vnet, but creates your VMSS in a brand new one.</p>
<p>If you are thinking about changing the Vnet of a VMSS after creation, think twice. The same way that you cannot move a VM from one Vnet to another today, you cannot move a VMSS from one Vnet to another today either.</p>
<p>If you want to try yourself, you can use Azure CLI to change the subnet (and Vnet) association of a VMSS like this:</p>
<pre>$ az vmss update -n myVmss --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].subnet='{"id":"/subscriptions/e7da9914-9b05-4891-893c-546cb7b0422e/resourceGroups/vmssTest/providers/Microsoft.Network/virtualNetworks/myVnet2/subnets/subnet2","resourceGroup": "vmssTest"}'
<span style="color:#ff0000;">VM scale set /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/vmssTest/providers/Microsoft.Compute/virtualMachineScaleSets/myVmss
cannot be updated to reference subnets from different virtual networks. 
Existing subnet reference: /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/vmssTest/providers/Microsoft.Network/virtualNetworks/myVnet2/subnets/subnet2, 
requested subnet reference: /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/vmssTest/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/subnet1.</span></pre>
<p>Note that I am not specifying the Resource Group because I configured a default group with the command "az configure --defaults group=vmssTest".</p>
<p>You can try the previous command with a running or with a stopped VMSS, with or without VMSS instances, unfortunately it will not make a difference.</p>
<p>Therefore, your best option is to deploy a VMSS with an ARM template, where you control every single attribute of a VMSS, including the Vnet and subnet. You can create your own template, alternatively I have a couple of templates in <a href="https://github.com/erjosito/IaC-Test">this Github repository </a>that create VMSS with and without Availability Zones, inbound NAT pools, external or internal load balancers, etc. For example, in order to create a VMSS in your own Vnet you could deploy the template like this:</p>
<pre>$ az group deployment create --template-uri https://raw.githubusercontent.com/erjosito/IaC-Test/master/vmssLinux_1nic_noVnet_noNsg_noNat.json --parameters @./vmss-parameters.json</pre>
<p>Or if using Windows, you do not need the "./":</p>
<pre>&gt; az group deployment create --template-uri https://raw.githubusercontent.com/erjosito/IaC-Test/master/vmssLinux_1nic_noVnet_noNsg_noNat.json --parameters @vmss-parameters.json</pre>
<p>Or if you want to have a VMSS with an existing NAT pool associated, you can use the "vmssLinux_1nic_noVnet_noNsg", instead of "vmssLinux_1nic_noVnet_noNsg_noNat":</p>
<pre>$ az group deployment create --template-uri https://raw.githubusercontent.com/erjosito/IaC-Test/master/vmssLinux_1nic_noVnet_noNsg.json --parameters @./vmss-parameters.json</pre>
<p>The parameters file could look something like this:</p>
<pre>{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
            "vmCount": { "value": 2 },
            "vmName": { "value": "myVm" },
            "vmSize": { "value": "Standard_D1_v2" },
            "vmssName": { "value": "myVmss" },
            "vnetName": { "value": "myVnet" },
            "subnet0Name": { "value": "subnet1" },
            "vmUsr": { "value": "admin-user" },
            "vmPwd": { "value": "Microsoft123!" },
            "deployLBYesNo": { "value": "yes" },
            "lbSku": { "value": "basic" },
            "lbType": { "value": "external" },
            "azYesNo": { "value": "no" },
            "scriptUri": { "value": "https://raw.githubusercontent.com/erjosito/IaC-Test/master/ubuntu_vm_config.sh" },
            "scriptCmd": { "value": "sh ubuntu_vm_config.sh" },
            "capacityMax": { "value": 10 },
            "capacityMin": { "value": 2 },
            "capacityDef": { "value": 1 },
            "cpuMax": { "value": 60 },
            "cpuMin": { "value": 30 }
        }
}</pre>
<p>Obviously you would change parameters to suit your needs, or refer to your Azure Key Vault for security-sensitive information such as the username and password.</p>
<p>But what if you would like to change stuff like load balancing or inbound NAT? Having to redeploy the VMSS just to add an additional LB rule sounds like an overkill. Fortunately, in this case you can actually modify the configuration of a running VMSS. For example, this Azure CLI command in Linux adds two inbound NAT pools ("sshPool" and "webPool", defined in an Azure Load Balancer called "myVm-ext-slb"):</p>
<pre>$ az vmss update -n myVmss --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools='[{"id":"/subscriptions/111111-1111-1111-1111-111111111111/resourceGroups/vmssTest/providers/Microsoft.Network/loadBalancers/myVm-slb-ext/inboundNatPools/sshPool","resourceGroup": "vmssTest"},{"id":"/subscriptions/111111-1111-1111-1111-111111111111/resourceGroups/vmssTest/providers/Microsoft.Network/loadBalancers/myVm-slb-ext/inboundNatPools/webPool","resourceGroup": "vmssTest"}]'</pre>
<p>And in Windows:</p>
<pre>&gt; az vmss update -n myVmss --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools="[{\"id\":\"/subscriptions/111111-1111-1111-1111-111111111111/resourceGroups/vmssTest/providers/Microsoft.Network/loadBalancers/myVm-slb-ext/inboundNatPools/sshPool\",\"resourceGroup\": \"vmssTest\"},{\"id\":\"/subscriptions/111111-1111-1111-1111-111111111111/resourceGroups/vmssTest/providers/Microsoft.Network/loadBalancers/myVm-slb-ext/inboundNatPools/webPool\",\"resourceGroup\": \"vmssTest\"}]</pre>
<p>Note that the previous command completely replaces the previous value of the loadBalancerInboundNatPools property, so if you already had some pools associated with your VMSS, do not forget to include them as well!</p>
<p>Now you can check the automatically created rules in the load balancer with this command:</p>
<pre>$ az network lb inbound-nat-rule list --lb-name myVm-slb-ext -o table
 BackendPort FrontendPort IdleTimeoutInMinutes Name      Protocol ProvisioningState ResourceGroup
------------ ------------ -------------------- --------- -------- ----------------- -------------
          22        50102                    4 sshPool.7 Tcp      Succeeded         vmssTest
          22        50103                    4 sshPool.8 Tcp      Succeeded         vmssTest</pre>
<p>As you can see, only rules for the sshPool are there. That is because we already had the sshPool before, and we added the webPool new. But just changing the VMSS configuration will not alter running instances. In order to update all instances to pick up the new VMSS configuration, you need to run a command like this:</p>
<pre>$ az vmss update-instances --instance-ids '*' --no-wait -n myVmss</pre>
<p>Note that you would not do this in production, since all instances are brought down at the same time, but for a test environment it provides quicker results. Now you can check the Load Balancer rules, and you should see the new entries alright.</p>
<pre>$ az network lb inbound-nat-rule list --lb-name myVm-slb-ext -o table
 BackendPort FrontendPort IdleTimeoutInMinutes Name      Protocol ProvisioningState ResourceGroup
------------ ------------ -------------------- --------- -------- ----------------- -------------
22 50102 4 sshPool.7 Tcp Succeeded vmssTest 80 50002 4 webPool.7 Tcp Succeeded vmssTest 22 50103 4 sshPool.8 Tcp Succeeded vmssTest 80 50003 4 webPool.8 Tcp Succeeded vmssTest

Hope this post helped you to better understand how to change network parameters (except the Vnet) of an existing VMSS. Thanks for reading!

&nbsp;

