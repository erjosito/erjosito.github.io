---
layout: post
title: Dynamic DNS with Azure DNS (and a bash script)
date: 2017-08-07 14:42:14.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- azure
tags:
- azure cli
- azure dns
- bash
- bash automation
- cloud
- cloud dns
- dns
- dynamic dns
- service principal
meta:
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _thumbnail_id: '1030'
  _wpas_skip_14855412: '1'
  _publicize_job_id: '8001089325'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2017/08/07/dynamic-dns-with-azure-dns-and-a-bash-script/"
---
As you are probably aware of, DNS is one of the services offered in Azure (you can read more about it [here](https://docs.microsoft.com/en-us/azure/dns/), pricing [here](https://azure.microsoft.com/en-us/pricing/details/dns/)). And as you probably know as well, you can programatically manage Azure resources over many different ways.

During the weekend it struck me that I could migrate the dynamic DNS solution I had before to Azure. The process is quite simple: a script is executed from time to time, and verifies that the IP address configured for a certain FQDN is the current public IP address assigned to a connection. And it case it is not, the IP address in the name server should be updated.

I was not sure whether to write a blog about this, since this is so much simpler as other stuff that people are doing out there, but I decided to go for it, because this example illustrates a couple of important things:

- This is an example of how Azure partners can offer their added value out of standard Azure services with just a bit of additional code
- You can (and probably should) automate anything in Azure
- You can use service principal authentication in your scripts limited to the resources your app needs access to (see below for more info on how to do this)
- You can use the Azure CLI to implement Azure automation in Linux or Mac OS X (although there are other ways like REST API, Python SDKs, etc)

I implemented the DNS automation script with bash, leveraging Azure CLI 2.0 (you can download the script from my Github repo┬á[here](https://github.com/erjosito/stuff/blob/master/updateDNS.sh)). As you can see it is quite simple (other than the usual checks to verify that everything works as it should), and the Azure CLI commands to interact with the DNS recordset are intuitive enough. To remove a record (in other words, an IP address) from an A recordset:

```
az network dns record-set a remove-record┬á-n $recordsetName -z $zoneName --ipv4-address $myDnsIp
```

And similarly, to add a new record (an IP address) to an A recordset:

```
az network dns record-set a add-record┬á-n $recordsetName -z $zoneName --ipv4-address $myDnsIp
```

There is a point that deserves additional clarification, and that is how the script authenticates to Azure. It is using Service Principals, which ideally are created dedicated for your script. A Service Principal is a set of credentials (a GUID and a digital certificate or a password) that applications or scripts can leverage in order not to require a user to authenticate every time they run (and so that you do not have to hard code your username and password in a script). You can find more information about service principals [here](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects).

In my case, I am using password authentication, my recommendation would be using digital certificates for a better security. Here the Azure CLI command that you can use to create your application credentials:

```
**az ad app create --password ThisIsTheSuperSecretPassword --display-name myDynDNSapp --homepage mydyndns.com --identifier-uris mydyndns.com** { "appId": "11111111-1111-1111-1111-111111111111", "appPermissions": null, "availableToOtherTenants": false, "displayName": "myDynDNSapp", "homepage": "mydyndns.com", "identifierUris": ["mydyndns.com"], "objectId": "22222222-2222-2222-2222-222222222222", "objectType": "Application", "replyUrls": [] }
```

You need to give a couple of URLs for the app definition, you can put there whatever you want. It is important that you write somewhere the password you specified (again, a digital certificate would have been a better idea, conceded), and the appId that has been assigned to your application, since those are the values you will need in order to authenticate to Azure. Now we need to create a service principal associated to the app. Here how you can do it:

```
**az ad sp create --id 11111111-1111-1111-1111-111111111111** { "appId": "11111111-1111-1111-1111-111111111111", "displayName": "myDynDNSapp", "objectId": "33333333-3333-3333-3333-333333333333", "objectType": "ServicePrincipal", "servicePrincipalNames": ["11111111-1111-1111-1111-111111111111", "mydyndns.com"] }
```

As you can see, you need to specify the appId as an argument to create the Service Principal. Lastly, we need┬áto give our service principal enough privilege to modify our DNS zone (but not more). For this I will configure the default resource group of the Azure CLI to the resource group where my DNS zone has been created (in this case, I named my resource group originally "DNS"), and then assign the privilege of contributor:

```
**az configure --defaults group=DNS**
```

```
**az role assignment create --assignee 33333333-3333-3333-3333-333333333333 --role contributor** { "id": "/subscriptions/\<your\_sub\_id\>/resourceGroups/ansiblelab/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666", "name": "66666666-6666-6666-6666-666666666666", "properties": { "principalId": "33333333-3333-3333-3333-333333333333", "roleDefinitionId": "/subscriptions/\<your\_sub\_id\>/providers/Microsoft.Authorization/roleDefinitions/77777777-7777-7777-7777-777777777777", "scope": "/subscriptions/\<your\_sub\_id\>/resourceGroups/DNS" }, "resourceGroup": "ansiblelab", "type": "Microsoft.Authorization/roleAssignments" }
```

Note how the scope is only the "DNS" resource group, so that our script only has permissions to modify objects contained in that resource group.

And that is basically it, now the only thing you need to do is to run your script periodically with cron in your laptop, your Linux-based home router, or somewhere else.

Thanks for reading down to here, comments are very welcome!

&nbsp;

