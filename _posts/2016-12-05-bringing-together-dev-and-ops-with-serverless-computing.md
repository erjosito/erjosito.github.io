---
layout: post
title: Bringing together Dev and Ops with Serverless Computing  (Azure Functions)
date: 2016-12-05 10:01:09.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- azure
tags: []
meta:
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/805698528001593345";}}
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '29639728974'
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2016/12/05/bringing-together-dev-and-ops-with-serverless-computing/"
---
You might have read my [previous blog](https://1138blog.wordpress.com/2016/08/16/a-recipe-for-devops-apis-collaboration-analytics/) on how important collaboration software can be for creating an effective DevOps culture. A couple of things have happened since I wrote that blog:

- Azure Functions make serverless computing possible in Azure
- Microsoft has launched Teams, more or less playing in the same category as Cisco Spark and Slack (do not expect a competitive comparison in this blog).
- I have a different logo on my business cards

Out of those three, ┬áthe first one is the most crucial┬áone, IMHO. Azure Functions allow to write code that will be executed upon a certain event. This event can be a number of different things, such as the addition of new data┬áto Azure storage, a new version of the contents of a GitHub repository, or a generic REST call. See [here](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings) for more details about the triggers supported by Azure Functions.

Why is this called "serverless"? Because you don't need to care about where that code is running. Whether it is a single small VM or hundreds of them running in parallel, Azure will take care of it, and charge you only by the amount of times your Azure Function is executed.

What that code does upon being called can be anything. For example, it could be another REST call, maybe to post information about the event that triggered it in a collaboration tool such as Microsoft Teams.

Why would you want to do that? To bridge the gap between Dev and Ops, between infrastructure admins and developers. To let developers know when infrastructure changes, and to let infrastucture admins know when code changes.

So now that we know why we want to do that, let's see how. First of all I would like to acknowledge Andreas Helland's blog [here](https://contos.io/azure-functions-and-microsoft-teams-e4a0b335dcfc) for the code on posting messages to Teams from Azure Functions.

The first thing we need to do is to enable posting to a certain channel in Teams. In order to do that, you need to add a "Connector" to that channel. As you can see in this picture, I added an Incoming Webhook in my channel "Incoming" (incidentally, I called it "Bosss"):

![teams-adding-web-hook]({{ site.baseurl }}/assets/images/teams-adding-web-hook.png)

When you configure the Webhook, you will be given an URL where to post content in order to send messages to the Teams channel. You can test with any REST client such as Postman in order to verify that your Webhook is working. You can use this payload in your POST call:

```
{"text": "Hello World"}
```

Now all we need to do is defining an Azure Function that runs that REST call whenever something happens. Now Azure Functions are made out of two components: the trigger and the code.

As trigger we will use the addition of a new blob to a storage account. You can define this using Azure's GUI, and that will generate a function.json file such as this one:

```
{ "bindings": [{ "name": "myBlob", "type": "blobTrigger", "direction": "in", "path": "mycontainer/{fileName}", "connection": "\<here\_goes\_your\_storage\_account\_name\>\_STORAGE" }], "disabled": false }
```

Essentially, here you are binding information about the event (called "myBlob") so that it can be leveraged by the Azure Function code. In this case, whenever a blob is created in the "mycontainer" container in the specified storage account, the variable "fileName" will be passed to the Azure Function containing the blob's name.

Finally, here is what the Azure Function's code looks like. Azure Functions support a number of programming┬álanguages, I am using here C#:

```
#r "Microsoft.WindowsAzure.Storage" #r "System.Web" #r "System.Runtime" #r "System.Threading.Tasks" #r "System.IO" using System; using System.Net.Http.Headers; using System.Collections.Generic; using System.IO; public static void Run (Stream myBlob, string fileName, TraceWriter log) { using (var client = new HttpClient()) { client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json")); client.DefaultRequestHeaders.Add("User-Agent", "AzureFunctions"); // This is the URL from your Teams WebHook var url = "https://outlook.office365.com/webhook/\<copy from your Teams WebHook\>"; // Build the message, send it, log it var msgString = $"New blob {fileName} of {myBlob.Length} bytes"; StringContent msgJson = new StringContent ("{'text': '" + msgString + "'}"); ┬á log.Info(TeamMsg.ReadAsStringAsync().Result); HttpResponseMessage response = client.PostAsync(url,msgJson).Result; var responseString = response.Content.ReadAsStringAsync().Result; log.Info(responseString); } }
```

As you can see the code is pretty simple, the only thing it does is some logging and sending POST calls to the URL defined in Teams with information about the event that triggered the call. The message sent looks like this:

![teams-boss-message]({{ site.baseurl }}/assets/images/teams-boss-message.png)

What did we do? We brought information about an infrastructure change into a collaborative space such as a channel in Microsoft Teams, where developers and sysadmins can work together. Remember this is only an example, the next thing might┬ábe bringing information about a code change (for example using Github to trigger the Azure Function) to that collaborative space.

This way you can put developers and sysadmins on their path for collaborative work, setting up the basis for true DevOps.

&nbsp;

