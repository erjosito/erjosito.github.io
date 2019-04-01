---
layout: post
title: Verifying Azure Backup for on-premises machines with Powershell
date: 2017-05-30 23:34:01.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- arm
- azure
tags:
- automation
- azure backup
- backup
- mars
- mars agent
- powershell
- restore
meta:
  publicize_twitter_user: erjosito
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/869683350843162625";}}
  _thumbnail_id: '875'
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '5609180744'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2017/05/30/verifying-azure-backup-for-on-premises-machines-with-powershell/"
---
You have probably heard about Azure Backup (if you haven't, check the documentation [here](https://docs.microsoft.com/en-us/azure/backup/backup-introduction-to-azure-backup)). And you probably know that it is one of the most popular entry doors into the public cloud, since it offers cheap, simple, scalable, and unlimited storage for on-premises workloads (as well as for Azure VMs, of course).

The use case of backing up Windows systems is especially interesting, since all you need to do is installing a little software agent in the machine to be protected (originally named Azure Backup Agent, also known as Microsoft Azure Recovery Services agent or MARS), and you are ready to rumble, without having to install any additional infrastructure at all. You could use the optional┬áMicrosoft Azure Backup Server┬á(also provided at no cost) to provide more functionality, but in this blog I will focus on the agent-based solution.

My esteemed colleage Karl Davies-Barret (also known as [@AzureJedi](https://twitter.com/AzureJedi), I will always hate him for getting that Twitter handle before me) posed me today an interesting question: what degree of automation could we get using PowerShell for on-premises Windows machines protected by the Azure Backup Agent? Could you verify that backups are being taken correctly? Could you even try to restore a sample file, to make sure that the restore process works?

Well, your first choice is obviously checking the Azure Backup Vault itself with the ARM Powershell module. [Here](https://docs.microsoft.com/en-us/azure/backup/backup-client-automation) you can check how to automate with Powershell the installation and configuration of the Backup Agent on a Windows system. In this post I will assume that you have already installed the MARS agent in some of your on-premises Windows machine. Now you want to check the state of your backups.

First, get a list of the on-premises Windows machines being backed up to a vault with these commands:

```
# Variables $subName = "Visual Studio Enterprise" $vaultName = "MySurfaceBook" # Login Add-AzureRmAccount Select-AzureRmSubscription -SubscriptionName $subName # Get vault $vault = get-azurermrecoveryservicesvault -Name $vaultName # Set Vault context Set-AzureRmRecoveryServicesVaultContext -Vault $vault # Get backed up Windows systems $machines = Get-AzureRmRecoveryServicesBackupContainer -ContainerType Windows -BackupManagementType MARS
```

Now you have a list of your machines. You can connect to them using WinRM (Windows Remote Management, see [here](https://msdn.microsoft.com/de-de/library/aa384426%28v=vs.85%29.aspx) for more info) and check the state of the backups from there, or even initiate restore operations. You can verify that WinRM is enabled with these commands (as administrator):

```
Get-Service -Name WinRM Enable-PSRemoting -force
```

And now we can use the Online Backup PowerShell commands (reference [here](https://technet.microsoft.com/de-de/library/hh770400.aspx)) to get backup information, or even to restore files. For example, you could retrieve a list of the existing recovery points like this:

```
Invoke-Command -Session $s -ScriptBlock { Get-OBRecoverableItem -source $(Get-OBRecoverableSource)[0] | ft Name,PointInTime }
```

Note that this list is ordered from most recent to least recent, so getting the most recent recovery time isn't too difficult: just get the first element of the list.

You could even verify that restore works, by just restoring a random file. The following code (that you could call remotely through Invoke-Command) does just that:

```
# Recursive function that will browse the recovery point until # finding a file (skipping directories) function getFirstBackupFile ($dir) { $files = Get-OBRecoverableItem ($dir) if ($files[0].IsDir) { return getFirstBackupFile ($files[0]) } else { return $files[0] } } # Get the recovery points $sources = Get-OBRecoverableSource $rps = Get-OBRecoverableItem -Source $sources[0] # Select a random file out of the most recent RP $sampleFile = getFirstBackupFile ($rps[0]) # Restore the file $targetDir = "C:\Temp" $recoveryOptions = New-OBRecoveryOption -DestinationPath $targetDir -OverwriteType Overwrite Start-OBRecovery -RecoverableItem $sampleFile -RecoveryOption $recoveryOptions # Find restored file, and check that the file size matches $targetPath = $targetDir + "\" + ($sampleFile.ItemNameFriendly -replace ":","\_vol") if ($(get-item $targetPath).Length -eq $sampleFile.ItemSize) { Write-Host "File restored successfully, sizes match" } else { write-host ("File does not restored successfully, sizes do not match: " + $(get-item $targetPath).Length + " VS " + $sampleFile.ItemSize) }
```

Not only you have checked that your backups are being taken correctly, but you have verified┬áthat the restore operation works as it should. And you have done this programmatically for all Windows machines being backed up to a certain Azure vault.

