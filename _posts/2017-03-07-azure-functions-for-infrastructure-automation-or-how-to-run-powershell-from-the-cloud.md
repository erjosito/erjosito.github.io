---
layout: post
title: Azure Functions for Infrastructure Automation (or how to run PowerShell from
  the cloud)
date: 2017-03-07 17:34:44.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- azure
- functions
- powershell
tags:
- automation
- azure functions
- ssh
meta:
  _thumbnail_id: '655'
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '2604310483'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/839152348345819136";}}
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2017/03/07/azure-functions-for-infrastructure-automation-or-how-to-run-powershell-from-the-cloud/"
---
From my previous posts you have probably realized that I am a huge fan of infrastructure automation, since in my opinion it is the only way of operating efficiently a data center. In order to implement automation, you have two possibilities

1. Use an automation software, either installed locally in your premises (such as Microsoft's System Center Orchestrator, VMware's vRealize Automation or Cisco's UCS Director) or in the cloud (such as Microsoft's Azure Automation or Operations Management Suite)
2. Use your own scripts

I will focus in this blog in option 2. So far these scripts needed to reside in a server somewhere in your data center. Someone needed to take care of that server, to┬áensure that it was always up and had enough capacity,┬áinstalling and maintaining the required runtimes and dependendencies, etc.

Now think in the cloud. You could follow the same concept, having a VM where your scripts run from, or you could use a more elegant solution such as serverless computing. Microsoft's implementation of serverless computing is called "Azure Functions", and it is essentially just code that runs up there in the cloud. You just upload your script, and define when it should be run (periodically, upon calling a certain Web Hook, or other options).

Azure Functions support different programming languages, at the time of this writing bash, batch, C#, F#, JavaScript, PHP, PowerShell and Python. Your first decision is which language to use. Many infra admins that I have known use PowerShell for automation, since almost every vendor out there has PowerShell modules for their infrastructure (VMware vSphere, Cisco UCS, EMC, NetApp, etc).

The second choice is when to run your code. The triggers supported for PowerShell-based Azure Functions are HTTP (Web hooks), Queues (a message is posted to an Azure Storage Queue) or Timers (you define at what times and days your script should run, similar to a crontab job in Linux).

For example, think of the following scenario: you want to make sure that your developers power down their test machines every night. You could run a script every now and then that notifies them if their VMs are still up. Here you have example┬ácode:

```
Write-Output "PowerShell Timer trigger function executed at:$(get-date)"; # Variables $password = 'thisIsYourAppPassword' $clientId = '11111111-1111-1111-1111-111111111111' $tenantId = '22222222-2222-2222-2222-222222222222' $rg = 'MyResourceGroup' $vmName = 'my-VM-Name' # Authenticate to Azure with Service Principal and password. A more secure implementation # would be using digital certs instead of the password $secPassword = ConvertTo-SecureString $password -AsPlainText -Force $mycreds = New-Object System.Management.Automation.PSCredential ($clientId, $secPassword) Add-AzureRmAccount -Credential $mycreds -ServicePrincipal -TenantId $tenantId # Find out the state of the VM Write-Output "Finding the status for VM $vmName" $vmDetail = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName -Status $vmOn = $true foreach ($vmStatus in $vmDetail.Statuses) { if($vmStatus.Code.CompareTo("PowerState/deallocated") -eq 0) { $vmStatusDetail = $vmStatus.DisplayStatus $vmOn = $false } } $output = $vmName + " " + $vmStatusDetail Write-Output $output # Compose a Teams message Write-Output "Sending message to Teams" if ($vmOn) { $msg = "Did you maybe happen to forget to power off VM " + $vmName + "?" } else { $msg = "Well done, VM " + $vmName + " seems to be powered off alright" } $body = "{'text': '" + $msg + "'}" $uri = "https://outlook.office365.com/webhook/WEBHOOK\_URL\_FOR\_MICROSOFT\_TEAMS" Invoke-RestMethod -Method Post -Uri $uri -Body $body
```

There are two things to note: first, the script needs to authenticate using a Service Principal. This authentication can be password-based or certificate-based, where obviously digital certificates offer better security (this example uses password authentication, essentially because I am lazy). See how to configure service principals with passwords or digital certificates [here](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal). The numbers 11111111-etc and 22222222-etc are the application ID (aka client ID) and the service principal ID respectively.

Secondly, you can post to Microsoft Teams calling a REST API. Posting somewhere else (such as to Cisco Spark or to Slack) would not be too different. Find more about how to post messages to Microsoft Teams in my previous post [here](https://1138blog.wordpress.com/2016/12/05/bringing-together-dev-and-ops-with-serverless-computing/).

By now you might be wondering whether Azure Functions already have the PowerShell modules that your script needs. For example, let us move to a different use case: you want to connect over SSH to an existing VM and run some bash commands. If you try to run SSH PowerShell commands from your Azure Function, you will get some error messages, since there is no SSH module loaded per default. That can be changed though.

You can use FTP or Kudu in order to load additional PowerShell modules to your Azure Function. You can check out this very detailed [post](https://blogs.msdn.microsoft.com/powershell/2017/02/24/using-powershell-modules-in-azure-functions/)for the FTP by Steve Lee, and you can see how to connect to the Kudu console for Azure Functions [here](https://david-obrien.net/2016/07/azure-functions-kudu/). I personally prefer Kudu, since you do not need to set up any FTP credentials, and it supports drag and drop file transfers directly to your Internet browser window.

Azure Functions will automatically try to import all PowerShell modules located in the "modules" directory, you do not need to manually import the module (note how in the example further below there is no "Import-Module" command).

For SSH I chose the PowerShell module SSH-Sessions. You can find documentation and the module files [here](http://www.powershelladmin.com/wiki/SSH_from_PowerShell_using_the_SSH.NET_library). In case you are wondering, I tried with Posh-SSH but it gave out some errors when trying to access the allowed hosts in the Windows registry. Azure Functions are similar to Azure WebApps by not allowing access to the underlying operating system, so you can imagine that Posh-SSH's calls to the Windows registry were not particularly successful.

Now we have all we need. Here I am using SSH password authentication, you should probably be using SSH key authentication for better security. Here you have the code:

```
Write-Output "PowerShell Timer trigger function executed at:$(get-date)"; # Variables $hostname = "my-vm.westeurope.cloudapp.azure.com" $username = "myUser" $password = "myPassword" $cmd = "date" # Establish SSH session and run command $sessionId = 0 $timeout = 10 New-SshSession -ComputerName $hostname -Username $username -Password $password $session = Get-SSHSession -SessionId $sessionId if ($session.Connected -eq $True) { $cmdResult = Invoke-SSHCommand -ComputerName $hostname -Command $cmd -q Write-Output ('OUTPUT: ' + $cmdResult) } else { Write-Output "ERROR: Session could not be established" }
```

So there you go. We have seen how to interact from Azure Functions with Azure IaaS using the native functionality of the Azure ARM SDK already embedded in Azure Functions, and how to increase that functionality by importing new PowerShell modules into an Azure Function (SSH-Sessions in this example). Your automation runs "serverlessly" in the cloud now, you only need to take care of your code, not of the servers your code runs on.

