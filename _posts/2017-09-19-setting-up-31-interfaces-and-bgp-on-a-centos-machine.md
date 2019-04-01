---
layout: post
title: Setting up /31 interfaces and BGP on a CentOS machine
date: 2017-09-19 09:27:32.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- azure
- linux
tags:
- azure stack
- bgp
- centos
- linux networking
- linux router
- networking
- quagga
- virtual router
meta:
  _thumbnail_id: '1102'
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _wpas_skip_14855412: '1'
  _publicize_job_id: '9438804970'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2017/09/19/setting-up-31-interfaces-and-bgp-on-a-centos-machine/"
---
After reading the title above, you might be wondering why the heck you would want to do such a thing. The reason in my particular case is to connect an Azure Stack installation to an Azure ExpressRoute circuit, since Azure Stack needs some kind of connectivity to Azure. Azure Stack uses internally BGP and /31 interfaces, hence the need for those technologies. You could obviously use a virtual router of your favorite network vendor (such as Cisco CSR 1000v, which would be a great fit for this use case), but if you are on the low side of the budget, Linux is your friend. And since my favorite web search engine did not show me any hit explaining how to do it, hence this blog.

There are essentially 4 steps to this process:

1. Configure /31 interfaces in your Linux machine
2. Configure IP forwarding
3. Install and configure BGP

&nbsp;

## 1. Configure /31 Interfaces

This is the trickiest part of the process. I used a CentOS distro, if you are using something else like SUSE or Ubuntu your mileage might vary.

First, we should clarify what is a /31 address, and why they are used out there. /30 addresses are easy: only the 2 last bits are variable, and they render two usable IP addresses:

- 0: subnet address
- 1: first usable address
- 2: second usable address
- 3: broadcast address

Hence, if you for example use the subnet 192.168.31.0/30, your 2 usable addresses are .1 and .2. The subnet address will be .0, and the broadcast address .3. You could argue that these two IP addresses are not used in a point-to-point link (when there is only one other subnet member out there, broadcast is not that meaningful), so essentially you are wasting 50% of your IP address space.

[IETF RFC 3201](https://tools.ietf.org/html/rfc3021)┬áhas standardized the usage of /31 subnets for point-to-point links in order to minimize IP address waste, where by not having subnet or broadcast addresses, IP address space utilization is optimized. For example, you could use 192.168.31.0/31 (addresses .0 and .1) in one link, and 192.168.31.2/31 (addresses .2 and .3) in another one.

The main problem here is that for every Ethernet interface, NetworkManager in CentOS automatically calculates the broadcast IP address, since it assumes that the interface is point-to-multipoint (as most Ethernet interfaces are). For example, if you configure the IP address 172.16.31.2/31, Network Manager will configure the broadcast address to 172.16.31.3. When you try to ping the other side, CentOS will think that you are actually trying to broadcast:

```
[root@quagga-01 ~]# ifconfig eth1 eth1: flags=4163\<UP,BROADCAST,RUNNING,MULTICAST\> mtu 1500 inet 172.16.31.2 netmask 255.255.255.254 **broadcast 172.16.31.3**... [root@quagga-01 ~]# ping 172.16.31.3 **Do you want to ping broadcast?** Then -b. If not, check your local firewall rules.
```

You therefore need to disable NetworkManager in your ifcfg script with the directive NM\_CONTROLLED=no. You can set the broadcast address to something else, such as 172.16.31.255 (they actual value does not really matter, we just want to have it different than the IP address of the other side). Here is my ifcfg-eth1 for reference:

```
TYPE=Ethernet BOOTPROTO=none IPADDR=172.16.31.3 NETMASK=255.255.255.254 BROADCAST=172.16.31.255 DEFROUTE=no PEERDNS=no PEERROUTES=yes IPV4\_FAILURE\_FATAL=no IPV6INIT=no NAME=eth1 UUID=74960d0e-eb66-4c0b-92da-7ca1f9f3eef3 DEVICE=eth1 ONBOOT=yes **NM\_CONTROLLED=no**
```

If you restart the network (systemctl restart network) check the IP parameters of the interface, you should verify that the broadcast IP address is set accordingly, and now you can ping the other side:

```
[root@quagga-01 ~]# ifconfig eth1 eth1: flags=4163\<UP,BROADCAST,RUNNING,MULTICAST\> mtu 1500 inet 172.16.31.2 netmask 255.255.255.254 broadcast **172.16.31.255**... [root@quagga-01 ~]# ping 172.16.31.3 PING 172.16.31.3 (172.16.31.3) 56(84) bytes of data. 64 bytes from 172.16.31.3: icmp\_seq=1 ttl=64 time=3.74 ms 64 bytes from 172.16.31.3: icmp\_seq=2 ttl=64 time=0.285 ms
```

&nbsp;

## 2. Configure IP Forwarding

This one should be a piece of cake, but don't forget it or your Linux machine will not behave as a router. You will find many pages in Internet showing how to do this. You need to configure a system parameter in /etc/sysctl.conf (or in a file inside of the /etc/sysctl.d directory):

```
net.ipv4.ip\_forward = 1
```

Now reload changes youÔÇÖve made to the kernel parameters configuration:

```
systctl -p
```

You can verify whether IP forwarding is enabled (should return 1):

```
cat /proc/sys/net/ipv4/ip\_forward
```

&nbsp;

## 3. Install and Configure BGP

We will use the package quagga for this. Installation is easy enough (the reason of why we are installing telnet as well coming next):

```
yum install -y quagga telnet
```

Configuration is pretty easy too, but there are a few things about quagga you need to understand. Quagga is made out of multiple daemons, one for each routing protocol. The glue putting all together is [Zebra](https://en.wikipedia.org/wiki/GNU_Zebra). However, you do not start or stop any zebra daemon, but the individual routing protocol daemons (bgpd in our example).

You will then connect to a Cisco-CLI-like shell via telnet (that is why we installed telnet). But first things first. If you check your /etc/quagga directory, you will find two files: vtysh.conf (vtysh is the process providing the CLI) and zebra.conf. You can add a new configuration file for bgpd, starting from the samples you can find in┬á/usr/share/doc/quagga-0.99.22.4/. For example, my bgpd.conf file looks like this:

```
hostname quagga-01 password mysupersecretpassword enable password mysupersecretpassword ! router bgp 65001 bgp router-id 10.0.0.1 network 10.0.0.0/8 neighbor 172.16.31.3 remote-as 65002 log file bgpd.log ! log stdout
```

As you can see, this looks like a standard Cisco BGP configuration, so it should be familiar. Now you only need to configure the bgpd daemon to start, and you can connect to the router:

```
systemctl enable bgpd systemctl start bgpd
```

Now you can connect to your router and run your familiar BGP show commands to start playing around (if you are not sure about show commands, the question mark is your friend!):

```
[root@quagga-01 ~]# telnet localhost bgpd ... Password: quagga-01\> sho ip bgp summ BGP router identifier 10.0.0.1, local AS number 65001 RIB entries 1, using 112 bytes of memory Peers 1, using 4560 bytes of memory Neighbor V AS MsgRcvd MsgSent TblVer InQ OutQ Up/Down State/PfxRcd 172.16.31.3 4 65002 6 9 0 0 0 00:04:58 1 Total number of neighbors 1 quagga-01\>
```

&nbsp;

