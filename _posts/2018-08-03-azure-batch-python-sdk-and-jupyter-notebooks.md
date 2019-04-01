---
layout: post
title: Azure Batch Python SDK and Jupyter Notebooks
date: 2018-08-03 08:32:07.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- azure
- python
tags: []
meta:
  _thumbnail_id: '1674'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:55:"https://twitter.com/erjosito/status/1025283179954008065";}}
  publicize_linkedin_url: www.linkedin.com/updates?topic=6431048873254350848
  timeline_notification: '1533281531'
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '20686765214'
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
  _publicize_done_20423206: '1'
  _wpas_done_20548370: '1'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2018/08/03/azure-batch-python-sdk-and-jupyter-notebooks/"
---
Hi there,

You are probably aware that Azure provides SDKs for most services. And you probably know as well that these SDKs cover not only Microsoft frameworks such as .NET, but others such as Java or Python as well. I am in particular fond of the Python SDKs, due to my background and to a very interesting complement: Jupyter Notebooks.

You will probably know what Azure Batch is. If not, you can go to [this technical overview](https://docs.microsoft.com/en-us/azure/batch/batch-technical-overview) to find out more. It is essentially a managed scheduler that allows you to define "tasks" (for example a script or a Docker container) that will then run on an autoscalable pool of compute resources. You can create these pools and tasks via multiple UIs: the Azure Portal, the Azure CLI, a standalone client app called [BatchLabs aka BatchExplorer](https://github.com/Azure/BatchExplorer/releases), Powershell, and through SDKs that cover multiple programming languages, such as Python.

Now let's go back to Python and Jupyter Notebooks. If you don't know Jupyter Notebooks, well, you should (you can find more info [here](https://jupyter.org/)). You can think of them as a playground where you can write code in snippets called "cells", that you can execute individually, and therefore work on your code step by step. This makes Jupyter Notebooks a very comfortable platform to explore Python SDKs. In the following picture you can see how this Jupyter Notebook for Azure Batch looks like:

![batch_jupyter]({{ site.baseurl }}/assets/images/batch_jupyter.png)

If you want to start playing with the Python SDK for Azure Batch you have sample code at your disposal in this [Azure Github repo](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch). I took that code as starting point, and started modifying it in order to support additional features, for example container-enabled pools and container-based workloads.

So I ported (copy&paste) the code from one of the samples in the samples repo to a Jupyter notebook, and after doing my modifications I posted the result┬á[here](https://github.com/erjosito/azure-batch-python-sample). Here some learnings from the process, that are not so well documented:

- When creating container-enabled pools in Azure Batch (with Docker preinstalled in the nodes, a configured private/public Docker image repository, and optionally images prefetched), **only container tasks are then supported**. Standard bash-based tasks that are not run inside of a Docker container will fail.
- Container based tasks will override some settings in the container. For example:
  - **WORKDIR** : Azure Batch will set the working directory of containers to /mnt/batch/tasks/..., overriding the WORKDIR setting of the container. If you are using relative links in the container that could be a problem. You can issue by issuing the container runtime option ÔÇï--workdir="".
  - **CMD** : You need to supply a command\_line argument to each container task, and it is not optional. This will override the CMD option of the container. What if you want to run the CMD defined in the container, that was configured in the original Dockerfile? Easy: you pass an empty string as command\_line to the Azure Batch task
- You can use the Azure Batch pool information and compute node status to create a function that waits until a pool is ready to accept jobs
- In container-enabled pools you can define a start task that will initialize the compute nodes (you find an example of such an init task in the Jupyter Notebook, that runs a [compute\_node\_init.sh](https://github.com/erjosito/azure-batch-python-sample/blob/master/compute_node_init.sh) script on each compute node in the pool). You need to run this startup task as **elevated user** , in case you are doing stuff such as installing software packages.

The compute node init script I use installs blobfuse and mounts an Azure Storage Blob Container to the compute nodes. Unfortunately I had some permission problems to inject the blobfuse mount to the task containers (that per default do not run as root). I need to find some time in the future to investigate this in depth. Other than that, you can inject blobs to the container tasks, and export the files generated by each task as blobs (that would act as input to further tasks).

Something I want to investigate as well is the Azure Batch functionality for task dependencies, where Task A will run only after Task B is done. Stay tuned.

I hope I could give you some ideas about how to start playing around with Azure Batch and develop a workload scheduler based on Python for your Docker containers. Thanks for reading!

