---
layout: post
title: Cisco ACI and Linux containers
date: 2015-02-04 12:28:39.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- ciscoaci
- docker
tags: []
meta:
  tumblr_erjosito_permalink: http://erjosito.tumblr.com/post/110063660087/cisco-aci-and-linux-containers
  tumblr_erjosito_id: '110063660087'
  _wp_old_slug: '110063660087'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2015/02/04/cisco-aci-and-linux-containers/"
---
Some time ago I published a video in youtube describing a way to put together multiple workload types here:┬á[https://www.youtube.com/watch?v=kmBnpl5XwnM](https://www.youtube.com/watch?v=kmBnpl5XwnM). It describes how to coordinate the configuration of the physical network (Cisco ACI) and a virtual network (vSphere vSwitch, OVS with Linux containers), in two cases: where the virtual network configuration is driven from the ACI controller (as in the case of vSphereÔÇÖs DVS), or where the virtual network configuration is done independently of the ACI controller.

The point is showing that you can connect any kind of virtual workload, from any hypervisor to ACI, in the worst case you have to coordinate (possibly through orchestration software) configuration of the virtual and physical networks.

The reason for this post is to answer some questions I have received in the meantime about how does it work exactly with Linux containers, so here we go.

The only thing that an ACI leaf switch needs to recognise a packet as belonging to a specific network group (in ACI slang called End Point Group or EPG) is a specific encapsulation (VLAN or VXLAN today). In the future it might be possible using other packet attributes like IP or MAC, but let us forget that for the time being.

The problem is, per default at least in Docker, packets are not coming out of a Docker host with VLAN or VXLAN encapsulation. The default Docker implementation for the network is rather like the DSL router you have at home: an internal network is created inside of the host, where the containers are connected, and the docker network bridge will NAT (Network Address Translation) outgoing packets to the IP address of the Linux host. See more details here:┬á[https://docs.docker.com/articles/networking/](https://docs.docker.com/articles/networking/). For example, in the following diagram you can see how all packets out of the host will come from the same IP address 172.16.14.54.

<figure class=""><img class="alignnone size-full wp-image-879" src="%7B%7B%20site.baseurl%20%7D%7D/assets/images/tumblr_inline_nj8vplpy5k1tnwog6.png" alt="tumblr_inline_nj8vplPY5K1tnwog6" width="500" height="466"></figure>

To reach a certain container, you need to NAT specific ports of a container to the same or different ports in the ÔÇ£outside networkÔÇØ, so that the container is reachable. The same way as you would install a Web server in your private network at home, and you would make it reachable from the Internet.

This is not very practical, since there is no way to know to which container packets coming to or leaving the host belong. So if we want to have VLAN tagging for packets leaving a container, we need to modify the native Docker stack.

There is a way of telling Docker not to create any network construct for a specific container with the setting ÔÇônet=none. That allows us to create it as we want it, and do VLAN encapsulation. The goal is building the following: we want a container to be connected to a bridge that will only add VLAN encapsulation to the packets, and transmit them to the outside world:

![tumblr_inline_nj8vry0bpK1tnwog6]({{ site.baseurl }}/assets/images/tumblr_inline_nj8vry0bpk1tnwog6.png)

In this case packets from the container arrive to the bridge ÔÇ£br-vlan181ÔÇØ, that will tag all packets with VLAN 181 and send them out eth0 (that is represented with eth0.181).

There are some concepts we need to understand first, like network namespaces. A network namespace is a group of network settings that are independent from the rest of the network configuration in the Linux server. In this ÔÇ£subgroupÔÇØ of network settings you could have IP routes and virtual or physical NICs, without them interfering with the rest of IP routes or NICs in the system. Each Linux container has at least one NIC in its own network namespace, which is connected to the network space in the host.

How to connect a NIC in a network space with another NIC in another network space? With a ÔÇ£pipeÔÇØ. Pipes take every packet coming out of a NIC, and put it on another one. Pretty intuitive, isnÔÇÖt it?

So we need a NIC in the container network namespace (we will call it veth100103, another NIC in the default network namespace of the Linux host (we will call it veth103), and a pipe between them. We will then attach the NIC in the global network namespace to a bridge that will do the VLAN encapsulation (in this case ÔÇ£br-vlan181ÔÇØ).

This bridge could be a standard Linux bridge, or Open vSwitch, or any other layer-2 bridge that works in Linux. In this example I will use Open vSwitch.

By the way, the Linux container should see the NIC in his namespace not like veth100103, but like ÔÇ£eth0ÔÇØ.

So letÔÇÖs start :-)

First, let us create the VLAN bridge. With Open vSwitch, here are the commands you would issue to create a bridge and attach it to subinterface eth0.181:

> # Create dot1q subinterface, create a bridge and attach it (OVS)  
> ovs-vsctl add-br br-vlan181  
> ifconfig br-vlan181 up  
> ovs-vsctl add-port br-vlan181 eth0.181

With a Linux bridge, the commands would have looked like this:

> # Create dot1q subinterface, create a bridge and attach it (Linux bridge)  
> brctl addbr br-vlan181  
> ifconfig br-vlan181 up  
> brctl addif br-vlan181 eth0.181

Let us launch a container. I am using the following command in a bash script:

> # Launch container without network  
> /usr/bin/docker run -d ÔÇônet=none ÔÇôname $lxcname ÔÇôcidfile $cidfile ubuntu ping 127.0.0.1

The most important part is the ÔÇônet=none. As we saw, that tells Docker not to create any network object for this container. ÔÇôcidfile saves the container ID into a file (we will need this ID when allocating a network namespace to the container). I will have the container pinging itself for ever, for the sake of simplicity.

And now, let us create the network for the container. As we mentioned before, veth103 and veth100103 will be interconnected by a ÔÇ£pipeÔÇØ. That is the effect of the ÔÇ£peerÔÇØ option of the ip link command.

> # Create a pair of ÔÇ£peerÔÇØ interfaces A and B,  
> # bind the A end to the bridge, and bring it up  
> ip link add veth103 type veth peer name veth100103  
> ovs-vsctl add-port br-vlan181 veth103

Now we need to create a network namespace, and put veth100103 inside. In order to allocate the network namespace to the container, we need to create it with the specific PID of the container. You can get the containerÔÇÖs PID like this:

> # Find out containerÔÇÖs PID  
> pid=$(sudo docker inspect -f ÔÇÖ{{.State.Pid}}ÔÇÖ $1)

(where $1 is the name or the CID of the container)

And now we can create the network namespace:

> # Create Network Namespace and allocate to container  
> sudo mkdir -p /var/run/netns  
> sudo ln -s /proc/$pid/ns/net /var/run/netns/$pid

We have the network namespace, letÔÇÖs put veth10013 inside of it, and rename it to eth0

> # Place B inside the containerÔÇÖs network namespace,  
> # rename to eth0, and activate it with a free IP  
> ip link set veth100103 netns $pid  
> ip netns exec $pid ip link set dev veth100103 name eth0  
> ip netns exec $pid ip link set eth0 up  
> ip netns exec $pid ip addr add 192.168.1.101 broadcast 192.168.1.255 dev eth0  
> ip netns exec $pid ip route add default via 192.168.1.1

You can see that the ÔÇ£ip netnsÔÇØ command is pretty powerful. Not only it allows to set veth100103 inside of the network namespace, but it allows to reconfigure it with the IP address we want for the container, to rename it to ÔÇ£eth0ÔÇØ, and even to add a default gateway.

Additionally, it even allows to send some commands to the container itself. For example:

> # Run a ping in the container to update switch MAC tables  
> ip netns exec $pid ping -c 5 192.168.1.1

will trigger a ping inside of the container. Isnt it cool? I had always wanted a way to send commands to a Linux container, now I can do it at least for network tasks.

And now we have all packets from the container going through the pipe to the OVS bridge and onto the network with VLAN 181, so that ACI will put it in the right End Point Group and it will be able to talk to its fellow containers and even to virtual machines or bare metal servers, just as any other first-class citizen in the DC.

Another alternative to using Linux bridges would be using the macvlan module, where you can get packets tagged in an easier way. But that is matter for another blog. The point here was to describe the technical feasibility of connecting Linux containers to a physical network.

Thanks for reading!

