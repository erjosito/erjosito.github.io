---
layout: post
title: ACI 1.3, NFM, CliQr, Austin and much, much more!
date: 2016-05-09 09:51:46.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- news
tags: []
meta:
  tumblr_erjosito_permalink: http://erjosito.tumblr.com/post/144090084562/aci-13-nfm-cliqr-austin-and-much-much-more
  tumblr_erjosito_id: '144090084562'
  _wp_old_slug: '144090084562'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2016/05/09/aci-13-nfm-cliqr-austin-and-much-much-more/"
---
Hi there! Amazing, another month has gone by, and there are a bunch of new stuff coming our way from the Cisco Nexus 9000 and ACI corner. Not only a new ACI software version with support for the new EX hardware, but a new way of managing VXLAN networks the easy way (awarded the Best of Interop for DC award), and of course, the wealth of innovation coming from the Openstack Summit in Austin. Not to mention the latest Cisco acquisition of CliQr, that we all are extremely excited about.

But letÔÇÖs start with it. This is in a nutshell what has been going on during the last 4 weeks around Cisco ACI and the Nexus 9000:

- New ACI software releases!
  - 1.3(1g) is on cisco.com, congratulations to the team! Apart from the highly anticipated EX hardware support, some great new features for vSphere, like intra-EPG isolation on AVS and VDS, and dynamic EPG assignment with VDS. Here the release notes:
    - Switch software, 11.3(1): [http://www.cisco.com/c/en/us/td/docs/switches/datacenter/aci/apic/sw/1-x/release/notes/aci\_nxos\_rn\_1131.html](http://www.cisco.com/c/en/us/td/docs/switches/datacenter/aci/apic/sw/1-x/release/notes/aci_nxos_rn_1131.html)
    - APIC, 1.3(1):http://www.cisco.com/c/en/us/td/docs/switches/datacenter/aci/apic/sw/1-x/release/notes/apic\_rn\_131.html
  - Another new ACI software release: 1.2(3c). Please notice this release is mainly a bug-fix 1.2 release. Here the release notes: [http://www.cisco.com/c/en/us/td/docs/switches/datacenter/aci/apic/sw/1-x/release/notes/apic\_rn\_123.html](http://www.cisco.com/c/en/us/td/docs/switches/datacenter/aci/apic/sw/1-x/release/notes/apic_rn_123.html)
- On the new product for managing VXLAN networks, Cisco Nexus Fabric Manager:
  - NFM wins Best of Interop DC Award! More here: [http://blogs.cisco.com/datacenter/cisco-nexus-fabric-manager-wins-best-of-interop-2016-data-center-award](http://blogs.cisco.com/datacenter/cisco-nexus-fabric-manager-wins-best-of-interop-2016-data-center-award)
  - NFM already available in cisco.com. Download the OVA for 1.1(1) here: [https://software.cisco.com/download/release.html?mdfid=286305124&softwareid=286307078&release=1.1%281%29&relind=AVAILABLE&rellifecycle=&reltype=latest&i=rm.](https://software.cisco.com/download/release.html?mdfid=286305124&softwareid=286307078&release=1.1%281%29&relind=AVAILABLE&rellifecycle=&reltype=latest&i=rm.) Note that the OVA should only be used for testing purposes, production NFM deployments are delivered as hardware appliances.
  - Find out more about Cisco NFM in [https://www.cisco.com/go/nexusfabricmanager](https://www.cisco.com/go/nexusfabricmanager)
- Cisco has closed the CliQr acquisition, and Cisco CloudCenter has been born. Some relevant content:
  - Cisco blog: [http://blogs.cisco.com/news/cisco-completes-acquisition-of-cliqr](http://blogs.cisco.com/news/cisco-completes-acquisition-of-cliqr)
  - CliQr web site with information about its integration with ACI: [http://www.cliqr.com/ciscoaci/](http://www.cliqr.com/ciscoaci/)
  - Cisco CloudCenter (aka CliQr) and ACI demo (4:22): [https://www.youtube.com/watch?v=35ssaqhF8tw](https://www.youtube.com/watch?v=35ssaqhF8tw)
  - VoD on stretched app deployment with ACI+CliQr (58:20): [http://www.cliqr.com/resources/landing-pages/stretched-application-deployment-with-cliqr-cisco-aci/](http://www.cliqr.com/resources/landing-pages/stretched-application-deployment-with-cliqr-cisco-aci/)
  - And if you want to play with it, there is an ACI+CliQr demo in dCloud now ([https://dcloud.cisco.com](https://dcloud.cisco.com)).
- New in Cisco.com:
  - Hadoop-as-a-Service CVD on UCS/ACI: [http://www.cisco.com/c/en/us/td/docs/unified\_computing/ucs/UCS\_CVDs/HaaS\_on\_Bare\_Metal\_with\_UCSDExpress\_on\_Cisco\_UCS\_Integrated\_Infrastructure\_for\_Big\_Data\_and\_ACI.html](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/HaaS_on_Bare_Metal_with_UCSDExpress_on_Cisco_UCS_Integrated_Infrastructure_for_Big_Data_and_ACI.html)
  - Apprenda+ACI solution overview: [http://www.cisco.com/c/en/us/solutions/collateral/data-center-virtualization/application-centric-infrastructure/solution-overview-c22-736553.html](http://www.cisco.com/c/en/us/solutions/collateral/data-center-virtualization/application-centric-infrastructure/solution-overview-c22-736553.html)
  - 1.2(2g) L4-L7 SG deployment guide: [http://www.cisco.com/c/en/us/td/docs/switches/datacenter/aci/apic/sw/1-x/L4-L7\_Service\_Graph\_Deployment\_Guide/b\_L4L7\_Service\_Graph\_Deploy\_ver122g.html](http://www.cisco.com/c/en/us/td/docs/switches/datacenter/aci/apic/sw/1-x/L4-L7_Service_Graph_Deployment_Guide/b_L4L7_Service_Graph_Deploy_ver122g.html)
  - All 1.1(4) release notes have been merged into one document: [http://www.cisco.com/c/en/us/td/docs/switches/datacenter/aci/apic/sw/1-x/release/notes/apic\_rn\_114.html](http://www.cisco.com/c/en/us/td/docs/switches/datacenter/aci/apic/sw/1-x/release/notes/apic_rn_114.html)
- Cisco Blogs:
  - Digital Transformation disrupting ITSM: [http://blogs.cisco.com/datacenter/how-digital-transformation-is-disrupting-it-service-management](http://blogs.cisco.com/datacenter/how-digital-transformation-is-disrupting-it-service-management)
  - Container Networking and ONUG: [http://blogs.cisco.com/datacenter/container-networking-in-full-bloom-at-onug-spring-2016](http://blogs.cisco.com/datacenter/container-networking-in-full-bloom-at-onug-spring-2016)
  - Hybrid Cloud Trio (CliQr+HX+N9K): [http://blogs.cisco.com/datacenter/hybrid-cloud-power-trio](http://blogs.cisco.com/datacenter/hybrid-cloud-power-trio)
  - Container networking: [http://blogs.cisco.com/datacenter/bring-application-intent-to-networking-containers](http://blogs.cisco.com/datacenter/bring-application-intent-to-networking-containers)
  - DCNM gets a new face: [http://blogs.cisco.com/datacenter/dcnm10](http://blogs.cisco.com/datacenter/dcnm10)
  - Cloud success factors: [http://blogs.cisco.com/datacenter/the-rounds-industry-experts-discuss-data-center-cloud-and-best-practices](http://blogs.cisco.com/datacenter/the-rounds-industry-experts-discuss-data-center-cloud-and-best-practices)
  - Hybrid IT / CliQr: [http://blogs.cisco.com/datacenter/hybrid-it-its-a-strategy-not-something-you-build](http://blogs.cisco.com/datacenter/hybrid-it-its-a-strategy-not-something-you-build)
- From the web:
  - AVI Networks case study with ACI: [https://avinetworks.com/media/pdfs/Cisco\_ACI\_Case\_Study.pdf](https://avinetworks.com/media/pdfs/Cisco_ACI_Case_Study.pdf)
  - Fortigate connectors, including the one for ACI: [https://www.fortinet.com/products-services/products/firewall/fortigate-connectors.html](https://www.fortinet.com/products-services/products/firewall/fortigate-connectors.html)
  - Article on Container Networking with Vipin and Balaji: [https://www.sdxcentral.com/articles/featured/cisco-balaji-sivasubramanian-vipin-jain-project-contiv/2016/05/](https://www.sdxcentral.com/articles/featured/cisco-balaji-sivasubramanian-vipin-jain-project-contiv/2016/05/)
  - Great blog ÔÇ£ACI and MeÔÇØ, last entry on Upgrade/Downgrade Sequences: [https://aciandme.wordpress.com/](https://aciandme.wordpress.com/)
  - Deploying NetScaler+ACI: [https://www.citrix.com/blogs/2016/04/21/deploying-netscaler-adcs-in-cisco-application-centric-infrastructure/](https://www.citrix.com/blogs/2016/04/21/deploying-netscaler-adcs-in-cisco-application-centric-infrastructure/)
  - External blog on Openstack+ACI: [http://www.snarchs.com/2016/04/cisco-aci-and-openstack-integration.html](http://www.snarchs.com/2016/04/cisco-aci-and-openstack-integration.html)
  - New blog entry from Michael Earls: [http://www.michaelearls.com/category/cisco-aci/](http://www.michaelearls.com/category/cisco-aci/)
  - OneConvergence launches NSD for Openstack, supporting ACI: [http://www.marketwired.com/press-release/one-convergence-launches-network-service-delivery-platform-for-openstack-2114497.htm](http://www.marketwired.com/press-release/one-convergence-launches-network-service-delivery-platform-for-openstack-2114497.htm)
  - Tufin integration with ACI: [https://www.tufin.com/blog/tufin-and-cisco-aci-policy-based-automation-for-the-hybrid-network/](https://www.tufin.com/blog/tufin-and-cisco-aci-policy-based-automation-for-the-hybrid-network/)
- Videos (a lot on Openstack, as you would expect after the Austin summit):
  - From the Openstack Summit in Austin:
    - Walmart showed how to integrate an Openstack network over MP-BGP: [https://www.youtube.com/watch?v=TpKVd0DgDwM](https://www.youtube.com/watch?v=TpKVd0DgDwM)
    - Cisco on the power of community-developed software: [https://www.youtube.com/watch?v=xTM0kFwT6Y8](https://www.youtube.com/watch?v=xTM0kFwT6Y8)
    - Openstack@Sungard with ACI (31:19): [https://www.youtube.com/watch?v=RJwQQ7TJuz8](https://www.youtube.com/watch?v=RJwQQ7TJuz8)
    - RH+Cisco Openstack Solutions (46:33): [https://www.youtube.com/watch?v=7RF\_EjI0KKk](https://www.youtube.com/watch?v=7RF_EjI0KKk)
  - Openstack+ACI series by Mike Cohen:
    - ACI and Mirantis Fuel (8:26): [https://www.youtube.com/watch?v=BHSYXLhjYH0](https://www.youtube.com/watch?v=BHSYXLhjYH0)
    - GBP and ACI (16:06): [https://www.youtube.com/watch?v=m6rqOdYBOtQ](https://www.youtube.com/watch?v=m6rqOdYBOtQ)
    - ACI and Openstack, operations and Troubleshooting (7:41): [https://www.youtube.com/watch?v=xz1IA3F9Fz0](https://www.youtube.com/watch?v=xz1IA3F9Fz0)
    - Connecting bare metal servers to neutron networks (5:19): [https://www.youtube.com/watch?v=TOXmIYVnPE0](https://www.youtube.com/watch?v=TOXmIYVnPE0)
    - F5 LBaaS and ACI (8:08): [https://www.youtube.com/watch?v=5SeRKBC4Ptw](https://www.youtube.com/watch?v=5SeRKBC4Ptw)
  - And one more on Openstack: Openstack+Cisco from, by Ifti Rathore (37:59): [https://www.youtube.com/watch?v=mHxogb83yiI](https://www.youtube.com/watch?v=mHxogb83yiI)
  - Critical Success Factors for Cloud Part 1, with Carlos Pereira (9:15): [https://www.youtube.com/watch?v=h9n2C2CBQZs](https://www.youtube.com/watch?v=h9n2C2CBQZs)
  - GTRI on ACI (53:13): [https://www.youtube.com/watch?v=WFY3kUwTAw4](https://www.youtube.com/watch?v=WFY3kUwTAw4)
    - And here the slides: [http://www.slideshare.net/GlobalTechnologyResourcesInc/cisco-aci-a-new-approach-to-software-defined-networking-60921348](http://www.slideshare.net/GlobalTechnologyResourcesInc/cisco-aci-a-new-approach-to-software-defined-networking-60921348)
  - ACI is coming, are you ready? Webinar by Sunset Learning (57:02): [https://www.youtube.com/watch?v=sJiOguEWBfY](https://www.youtube.com/watch?v=sJiOguEWBfY)
  - Unboxing an APIC: [https://www.youtube.com/watch?v=2YeVFPU8Lug](https://www.youtube.com/watch?v=2YeVFPU8Lug)
  - Some videos from CLMEL:
    - ACI+Openstack demo: [https://www.youtube.com/watch?v=7-jrTE0NiCQ](https://www.youtube.com/watch?v=7-jrTE0NiCQ)
    - ACI microsegmentation and L4-L7: [https://www.youtube.com/watch?v=\_\_7jp2gqg5Q](https://www.youtube.com/watch?v=__7jp2gqg5Q)
    - ACI Policy Mgmt: [https://www.youtube.com/watch?v=FXrEc56Etaw](https://www.youtube.com/watch?v=FXrEc56Etaw)

Thanks for reading! Did I forget anything?

