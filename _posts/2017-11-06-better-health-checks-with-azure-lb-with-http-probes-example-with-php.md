---
layout: post
title: Better health checks with Azure LB and HTTP probes (example with PHP)
date: 2017-11-06 16:30:27.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- azure
tags: []
meta:
  _thumbnail_id: '1496'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:14855412;s:54:"https://twitter.com/erjosito/status/927558812994850816";}}
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '11145258190'
  _publicize_done_14974324: '1'
  _wpas_done_14855412: '1'
  publicize_twitter_user: erjosito
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2017/11/06/better-health-checks-with-azure-lb-with-http-probes-example-with-php/"
---
Having a good health check mechanism in your load balancer is critical. If your load balancer is not able to recognize when servers are not able to process client requests, that will result in a poor customer experience.

For example, imagine if you just ping your web servers to check their availability, but the Web server is not able to reach its database any more for some reason (for example, wrong routing table or misconfigured firewall). Ping will still work (even the Web TCP ports will still be functional as well), but any client connection forwarded to the server will result in an error.

Azure Load Balancer supports TCP probes and HTTP probes (see more information on Azure Load Balancer probing [here](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview)). You can use HTTP probes not only to monitor Web server availability, but to add additional functionality to your health checking scheme.

The idea is having an HTTP probe polling a Web page which will do some checks before giving back a 200 return code. For example, the web page might check that certain daemons are running correctly in the system, or that certain hosts are reachable over the network.

For example, I use HTTP probes in my [Advanced Networking Lab](https://github.com/erjosito/azure-networking-lab).┬á In this case I verify that the virtual firewalls have Internet reachability, before including them in the firewall load balancing farm (sending one single ICMP echo request to "bing.com"). I decided to generalize the web page I am using there and publish it in [this Github repository](https://github.com/erjosito/ALB-probes).

As you can see, there is a very simple PHP page (obviously, you need to have a Web server such as Apache and PHP installed on your server), that optionally performs some operations before deciding whether returning a 200, or something else (I went for a 409, but you could choose any other HTTP return code, even in the 201-299 range). For completeness, here some sections of the code.

The first one is a brief section where you can quickly configure the tests that you want to perform:

```
\<?php $reachabilityTest = True; $hosts = array ("bing.com", "google.com"); $daemonTest = True; $daemons = array ("httpd", "sshd"); $localTCPTest = True; $ports = array ("22", "80"); ?\>
```

After that, different sections follow, each corresponding to one of the tests. Here the one for host reachability, that prints along the way some information for troubleshooting:

```
\<?php // Reachability test if ($reachabilityTest === True) { print ("\<h2\>Reachability Test\</h2\>\n"); $allReachable = true; print (" \<ul\>\n"); foreach ($hosts as $host) { $result = exec ("ping -c 1 -W 1 " . $host . " 2\>&1 | grep received"); print (" \<li\>" . $host . ": " . $result . "\</li\>\n"); $pos = strpos ($result, "1 received"); if ($pos === false) { $allReachable = false; break; } } print (" \</ul\>\n"); if ($allReachable === false) { // Ping did not work print (" \<p\>At least one target host does not seem to be reachable (" . $host . ")\</p\>\n"); } else { // Ping did work print (" \<p\>All target hosts seem to be reachable\</p\>\n"); } } ?\>
```

And finally, the test results are evaluated to either return a 200, or something else (in this case a 409, "Conflict"):

```
\<?php // Return code evaluation if ( (!($reachabilityTest) || $allReachable) && (!($daemonTest) || $allRunning) && ( !($localTCPTest) || $allOpen) ) { http\_response\_code (200); } else { http\_response\_code (409); } ?\>
```

For example, you might verify that your VM has Internet reachability by pinging some other host in your network, and that your required daemons are running correctly. Or in case of a Web server, you might run a simple SQL query to verify connectivity to the database.

If you are using Windows machines or prefer other scripting languages, you can have a look at this code to see the overall architecture.

Happy Load Balancing!

