---
layout: post
title: Automation with Azure CSP - CSP and ARM APIs authentication
date: 2017-05-10 10:32:49.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- arm
- azure
- csp
- powershell
tags:
- admin consent
- automation
- billing
- cloud solution provider
- csp api
- delegated admin
- microsoft
- python
- rest api
meta:
  _publicize_job_id: '4892611370'
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
  _rest_api_client_id: "-1"
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/862238997669707776";}}
  _rest_api_published: '1'
  _thumbnail_id: '716'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2017/05/10/automation-with-azure-csp-csp-and-arm-apis-authentication/"
---
If you are reading this post, you might already know what CSP stands for: Cloud Solution Provider, it is the preferred selling model for Microsoft partners today when dealing with Azure. The authentication model in CSP has essentially two levels: you have CSP-level users, typically employees working for the Cloud Solution Provider itself, and you have customer-level users, typically employees working for the CSP's customers. My favourite post describing this model is Kirill's one [here](https://blogs.technet.microsoft.com/hybridcloudbp/2016/06/08/identity-and-rights-management-in-csp-model/).

I have been looking lately into working with the CSP model through REST APIs. The CSP management tool, called Partner Center, offers a REST API, that can be used to retrieve information such as customers, subscriptions and consumption.

First challenge I found was the lack of ARM-related information in the resource consumption records: you can find the resource GUID, but not the resource URI or eventual ARM tags, which might be useful to classify resources inside of a subscription. However, you actually can find those infos in the utilization records, so I worked around this one by creating a "translation dictionary" containing ARM-related information contained in the utilization records. This way I can enrich the information contained in the consumption entries.

You can see how I did this in Python [here](https://github.com/erjosito/stuff/blob/master/csptest.py).

The second challenge was the following: imagine if you want to do something over the ARM REST API now. For example, you have identified a VM with a high consumption, and you would like to offer the user a possibility to shut it down. This shutdown would require a call to Azure's┬áARM API, it cannot be done over the CSP API. We do have now the URI for the VM resource, but we need ARM credentials in order to call the API.

For authenticating to the CSP API I have been using the Web App that you can create in Partner Center, so the question is whether this Web App (actually its client ID and key) can be used to authenticate to a customer's┬ásubscription to perform ARM-related operations.

As you might have guessed, the┬áanswer to this question is something like "Yes, but you need to do a couple of things first":┬áYou need to define this app as multitenant, and you need to create a service principal in the customer tenant associated to the app.

First things first: I don't know why this Web App is not cretaed as multitenant in the first place, but you can verify it in Azure's portal, logging in with your CSP admin credentials. You can now go to Azure Active Directory, App Registrations, select your CSP app (probably something like "Partner Center Web App"), then click on "Settings", and the last item in "Properties" will be the "Multi-tenanted" field. Check┬áout this screenshot for a visual reference:

![PartnerCenterApp]({{ site.baseurl }}/assets/images/partnercenterapp.png)

OK, first obstacle out of the way. Now we can configure the individual customer subscriptions to accept logins from this app. I don't know either why that is not the case by default, but there is a small "knob" you need to configure for each individual customer subscription.

If you are familiar with the delegated administration model in Azure, you already know that this "knob" is to create a┬áservice principal in each customer's tenant, and associate it to our app. If you are not familiar with it (like me), you can read more about it [here](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Since this activity is probably going to be performed a couple of times (actually, every time that a new subscription or a new customer is added to the Partner Center), I tried to automate it. Interacting with Azure AD programmatically is getting better, and now you can even do it with the PowerShell module for ARM. I created this script for adding a service principal to an existing customer subscription:

```
# Variable initialization $appId="GUID of your Partner Center Web App" $customerTenantId="customer tenant ID, not the CSP tenant ID" $customerSubscriptionId="customer subscription Id" # Login (with CSP admin credentials, for example jose@yourcspdomain.onmicrosoft.com) Add-AzureRmAccount # Select customer subscription Select-AzureRmSubscription -SubscriptionId $customerSubscriptionId -tenantid $customerTenantId # Verify no Service Principal exists for our app yet $principal = Get-AzureRmADServicePrincipal | ? ApplicationId -eq $appId # Create SP for multitenant app if ($principal.Count -eq 0) { $principal = New-AzureRmADServicePrincipal -ApplicationId $appid # Verify new Service Principal exists now $principal = Get-AzureRmADServicePrincipal | ? ApplicationId -eq $appId if ($principal.Count -eq 1) { Write-Host ("New service principal successfully created, OID " + $principal.Id) } else { Write-Host "Service Principal creation does not seem to have worked too well..." } } else { Write-Host ("There is already a service principal for app ID " + $appId + ', OID ' + $principal.Id + ', skipping service principal creation') } # Verify that no Role Assignment for our SP exists already $role = Get-AzureRmRoleAssignment | ? objectid -eq $principal.Id if ($role.Count -eq 0) { # Assign Owner role (or contributor, or something else) New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName $appid $role = Get-AzureRmRoleAssignment | ? objectid -eq $principal.Id if ($role.Count -eq 1) { Write-Host ("Role " + $role.RoleDefinitionName + " successfully assigned to service principal") } else { Write-Host "Role assignment does not seem to have worked too well..." } } else { Write-Host ("Service principal already has the role " + $role.RoleDefinitionName + " assigned, skipping role assignment") }
```

As you can see, it is just two steps (the code may appear more complicated, but it is essentially the verifications to check whether the objects already existed): create the service principal, and assign it a role (Owner in this example, you could use something not as optimistic).

Now you can use the Partner Center Web App credentials to log into the ARM API (you need to use the customer's tenant ID, not the CSP's tenant ID), and do ARM operations on the customer subscription's resources such as shutdown VMs. In my Python example [here](https://github.com/erjosito/stuff/blob/master/csptest.py) I do a basic listing of resource groups, but you can do anything that the ARM API supports, such as the previously described example of shutting down VMs.

Useful? Please let me know if you know of a better way of doing this, such as creating the service principal over REST, instead of over PowerShell, so that it can be embedded in the Python script (my next weekend project).

**Update** : if you want to use this PowerShell code as an Azure Function, as well as automate the creation of the customer subscription, you might want to read [this post](https://1138blog.wordpress.com/2017/06/25/using-python-with-the-csp-api-to-automate-customer-onboarding/).

