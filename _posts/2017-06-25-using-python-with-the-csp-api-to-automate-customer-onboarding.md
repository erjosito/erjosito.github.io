---
layout: post
title: Using Python with the CSP API to automate customer onboarding
date: 2017-06-25 22:56:05.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- azure
tags:
- azure csp
- azure functions
- powershell
- python
- rest
- rest api
meta:
  _thumbnail_id: '897'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/879095890941800449";}}
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '6479413293'
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2017/06/25/using-python-with-the-csp-api-to-automate-customer-onboarding/"
---
You are probably a Microsoft CSP partner if you are reading this, so you most likely already know that it stands for Cloud Solutions Provider. And you probably know too that you can interact with it over the Partner Center GUI, or through the Partner Center APIs.

Have you automated the deployment of customers, their Azure subscriptions and Azure services? If not, you might find this article useful.

Now there are a already a bunch of Partner Center SDKs out there: [C#](https://github.com/PartnerCenterSamples/Commerce-API-DotNet), [Java](https://github.com/PartnerCenterSamples/Commerce-API-Java), and even [PowerShell](https://github.com/Microsoft/PartnerCenterPowerShellModule). I am more the Python sort of person, so I decided to start up a Python module for interacting with the CSP Partner Center. [Here](https://github.com/erjosito/CSPpython)┬áyou have the link to my Github repository. Have a look at the documentation in the repository, since it explains the process to create the customer, add a subscription to it, and enable that subscription for app-based ARM login.

This module is far for being complete, but it rather tries to demonstrate how to use the partner center APIs. It includes a command-line shell with which you can try out things. One of the interesting things of this shell is that you can use the --verbose option for any command, which will show you exactly the REST API call and its corresponding response. For example, to create a new subscription:

```
sandbox\> add subscription -v VERBOSE - \*\*\*\*\*\*\*\*\*\*\*\*\*\*\* REST API CALL - BEGIN \*\*\*\*\*\*\*\*\*\*\*\*\*\*\* VERBOSE - URL (POST): https://api.partnercenter.microsoft.com/v1/customers/0b99aff9-6c7e-4d66-b3c7-d438bb80a0d2/orders VERBOSE - HEADERS: {"Content-Type": "application/json", "Accept": "application/json", "Authorization": "Bearer \<removed\>"} VERBOSE - PAYLOAD "{\"LineItems\": [{\"Friendly Name\": \"New offer\", \"PartnerIdOnRecord\": null, \"OfferId\": \"MS-AZR-0146P\", \"Attributes\": {\"ObjectType\": \"OrderLineItem\"}, \"LineItemNumber\": 0, \"SubscriptionId\": null, \"Quantity\": 1}], \"ReferenceCustomerId\": \"0b99aff9-6c7e-4d66-b3c7-d438bb80a0d2\", \"BillingCycle\": \"unknown\", \"Attributes\": {\"ObjectType\": \"Order\"}, \"CreationDate\": null, \"Id\": null}" VERBOSE - Calling REST API... VERBOSE - RETURN CODE: 201 VERBOSE - ANSWER: {"id":"45c4298d-852d-4c51-bda3-13c24148057d","referenceCustomerId":"0b99aff9-6c7e-4d66-b3c7-d438bb80a0d2","billingCycle":"monthly","lineItems":[{"lineItemNumber":0,"offerId":"MS-AZR-0146P","subscriptionId":"E433773A-ED07-4FCD-B7A2-5453D64704EC","friendlyName":"Microsoft Azure","quantity":1,"links":{"subscription":{"uri":"/customers/0b99aff9-6c7e-4d66-b3c7-d438bb80a0d2/subscriptions/E433773A-ED07-4FCD-B7A2-5453D64704EC","method":"GET","headers":[]}}}],"creationDate":"2017-06-25T13:27:27.33-07:00","links":{"self":{"uri":"/customers/0b99aff9-6c7e-4d66-b3c7-d438bb80a0d2/orders/45c4298d-852d-4c51-bda3-13c24148057d","method":"GET","headers":[]}},"attributes":{"etag":"eyJpZCI6IjQ1YzQyOThkLTg1MmQtNGM1MS1iZGEzLTEzYzI0MTQ4MDU3ZCIsInZlcnNpb24iOjF9","objectType":"Order"}} VERBOSE - \*\*\*\*\*\*\*\*\*\*\*\*\*\*\* REST API CALL - END \*\*\*\*\*\*\*\*\*\*\*\*\*\*\* sandbox\>
```

One application of using the Partner Center API is offering a self-service portal to potential customers, so that you can provision automatically the customer, an Azure subscription, and resources in that subscription.

As I wrote [here](https://1138blog.wordpress.com/2017/05/10/automation-with-azure-csp-csp-and-arm-apis-authentication/), you would need to run a couple of PowerShell commands on the newly created subscriptions in order to allow app-based ARM authentication. In order to automate that process, I created an Azure Function that can be called with a POST request, some data in the body, and the Azure Function will then create the service principal and a new role assignment in the newly created subscription.

If you want to know how this Azure function works, you can use again the --verbose option of the command-line app:

```
sandbox\> add role -v Please enter password for user removed@removed.onmicrosoft.com: VERBOSE - \*\*\*\*\*\*\*\*\*\*\*\*\*\*\* REST API CALL - BEGIN \*\*\*\*\*\*\*\*\*\*\*\*\*\*\* VERBOSE - POST to URL https://cspfunctions.azurewebsites.net/api/createContributorSP VERBOSE - NO HEADERS VERBOSE - DATA: {"subscription": "CCB8BB34-AD6F-43FE-9017-4B9720A66D40", "password": "removed", "user": "removed@removed.onmicrosoft.com", "appId": "6fba93c9-7ea3-4cf2-9a56-ef806a702dcb", "tenantId": "edae5676-508e-4380-bd6d-fa16fd1d0c8b"} Calling REST API... VERBOSE - RETURN CODE 200 VERBOSE - RESPONSE {"servicePrincipal":"New service principal successfully created, OID d7a1b9b3-308a-4208-8509-1b210bbfe68a","roleAssignment":"Role Contributor successfully assigned to service principal"} VERBOSE - \*\*\*\*\*\*\*\*\*\*\*\*\*\*\* REST API CALL - END \*\*\*\*\*\*\*\*\*\*\*\*\*\*\* New service principal successfully created, OID d7a1b9b3-308a-4208-8509-1b210bbfe68a Role Contributor successfully assigned to service principal sandbox\>
```

I haven't been able to figure out how to do the above (enabling app-based ARM login in a customer subscription) via native REST API Graph, PartnerCenter or ARM calls, if you have a hint about how to do this I would be very grateful.

Whenever I get time, I will try to put this in a nice Web App, of course hosted in Azure.

Thanks for reading down to here!

**Update** : my esteemed colleague Paulo Ramos made me aware of an alternative way of ┬ágetting a valid ARM token for the newly created customer subscription. Essentially, it involves using app+user authentication for the ARM API, providing as application ID a special GUID (1950a258-227b-4e31-a9cf-717495945fc2) that corresponds to Powershell, which is preconsented for every ARM subscription. You would send a POST request to the URL https://login.windows.net/{{CustomerId}}/oauth2/token┬áwith this body:

```
grant\_type=password& resource=https://management.azure.com/& username={{cspUser}}& password={{cspPassword}}& client\_id=1950a258-227b-4e31-a9cf-717495945fc2& scope=openid
```

&nbsp;

