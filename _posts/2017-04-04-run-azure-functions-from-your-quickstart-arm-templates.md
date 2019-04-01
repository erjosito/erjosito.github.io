---
layout: post
title: Run Azure Functions from your Quickstart ARM Templates
date: 2017-04-04 12:27:39.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- arm
- azure
- functions
tags:
- arm template
- azure function
- json
- quickstart arm template
- webhook
meta:
  _thumbnail_id: '658'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/849221932214079488";}}
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '3619371319'
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2017/04/04/run-azure-functions-from-your-quickstart-arm-templates/"
---
I was recently confronted with the following problem: how can I run code from inside an Azure ARM Quickstart Template? Well, if you don't know ARM Templates, they are essentially a declarative description of objects as you want to have them in Azure, but they don't allow to run code as such. For example, see [this question](https://social.msdn.microsoft.com/Forums/lync/en-US/6bf2a826-b205-401b-9506-29363ba0e7ad/how-to-trigger-a-logic-app-using-arm-templatejson?forum=azurelogicapps)┬áfrom someone who wanted to send an email notification upon completion of an ARM template.

The immediate answer would be something like creating Logic Apps or Azure Functions from the ARM Template. The problem there is that the template will create those Apps and Functions, but not necessarily run them. You could create them using a scheduled trigger, but then they would run multiple times, and you might want to have them executed only once.

My esteemed colleagued Mike Chen (see him in action [here](https://channel9.msdn.com/Shows/Tuesdays-With-Corey/Tuesdays-with-Corey-Azure-Resource-Policy)) put me on the right track: ARM Templates do not have a construct to call web APIs, but they do reach out for HTTP URLs when you nest other templates inside. For example, consider┬áthis quickstart template:

```
{ ┬á┬á┬á┬á"$schema":┬á"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", ┬á┬á┬á┬á"contentVersion":┬á"1.0.0.0", ┬á┬á┬á┬á"parameters":┬á{}, ┬á┬á┬á┬á"variables":┬á{ ┬á┬á┬á┬á┬á┬á┬á┬á"TemplateUri":┬á"https://erjositofunctions.azurewebsites.net/api/fakeARMtemplate" ┬á┬á┬á┬á}, ┬á┬á┬á┬á"resources":┬á[┬á┬á┬á┬á┬á┬á┬á┬á{ ┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á"name":┬á"myNestedTemplate", ┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á"type":┬á"Microsoft.Resources/deployments", ┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á"apiVersion":┬á"2016-06-01", ┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á"properties":┬á{ ┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á"mode":┬á"Incremental", ┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á"templateLink":┬á{ ┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á"uri":┬á"[variables('TemplateUri')]", ┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á"contentVersion":┬á"1.0.0.0" ┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á}, ┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á"parameters":┬á┬á{} ┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á┬á} ┬á┬á┬á┬á┬á┬á┬á┬á} ┬á┬á┬á┬á] }
```

As you have probably realized, this template does nothing else than running another template that is nested inside, and that is reachable under the URL [https://erjositofunctions.azurewebsites.net/api/fakeARMtemplate](https://erjositofunctions.azurewebsites.net/api/fakeARMtemplate). If you call that link you will get JSON code for an empty ARM template that does nothing at all:

```
{ '$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#', 'contentVersion': '1.0.0.0', 'parameters': {}, 'variables': {}, 'resources': [] }
```

So essentially, our initial ARM template calls a URL and downloads another ARM template that does not do anything. Exciting isn't it?

Well, actually the downloaded ARM template does not do anything, but that does not mean that nothing happens. As you have probably figured out from the URL, when you are calling┬á[https://erjositofunctions.azurewebsites.net/api/fakeARMtemplate](https://erjositofunctions.azurewebsites.net/api/fakeARMtemplate) you are actually running an Azure Function. Here is the function, written in C#:

```
#r "System.Web" #r "System.Runtime" #r "System.Threading.Tasks" #r "System.IO" using System; using System.Net; using System.Net.Http.Headers; using System.Collections.Generic; using System.IO; public static async Task Run(HttpRequestMessage req, TraceWriter log) { log.Info("C# HTTP trigger function processed a request."); // Get request body dynamic data = await req.Content.ReadAsAsync(); ┬á //Do whatever you want to do, for example call a webhook with JSON payload using (var client = new HttpClient()) { client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json")); client.DefaultRequestHeaders.Add("User-Agent", "AzureFunctions"); var webhookUri = "https://myWebHook.com"; StringContent webhookBody = new StringContent ("{'text': 'hello World'}"); HttpResponseMessage response = client.PostAsync(webhookUri,webhookBody).Result; var responseString = response.Content.ReadAsStringAsync().Result; } //Send back an empty template as JSON code, as answer to the original GET var template = @"{'$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#', 'contentVersion': '1.0.0.0', 'parameters': {}, 'variables': {}, 'resources': []}"; HttpResponseMessage myResponse = req.CreateResponse(HttpStatusCode.OK); myResponse.Content = new StringContent(template, System.Text.Encoding.UTF8, "application/json"); return myResponse; }
```

As you can see, other than returning the JSON string with an empty, but syntactically correct ARM template, the Azure Function is calling a Webhook. This could do anything, from triggering another Azure Function, or an Azure Logic App, or whatever your imagination can think of.

Now, the only thing you need to do is adding a┬ánested template deployment pointing to your Azure Function in your existing ARM template (that hopefully does do something), and your Azure Function will run every time the ARM template is deployed.

Your might be wondering now how to pass parameters to the Azure Function. Well, the master template will try to GET the nested template, so you cannot use POST. So you need to pass the arguments in the URI. For example, if you wanted to pass the resource group name where the ARM template is being deployed to, you could use this definition of the URI:

```
"TemplateUri": "[concat ('https://erjositofunctions.azurewebsites.net/api/fakeARMtemplate', '?resourcegroupname=', resourceGroup().name)]"
```

And your Function (in this example in C#) would have to extract the parameter to a variable, for example like this:

```
// Parse query parameter resourcegroupname string resourceGroupName = req.GetQueryNameValuePairs() .FirstOrDefault(q =\> string.Compare(q.Key, "resourcegroupname", true) == 0) .Value;
```

Now you can use the resourceGroupName variable inside of your code, or you can pass it forward to your webhook.

Have fun with ARM and Azure Functions!

