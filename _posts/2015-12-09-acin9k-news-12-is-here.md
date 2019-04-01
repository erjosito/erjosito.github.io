---
layout: post
title: 'ACI/N9K news: 1.2 is here!'
date: 2015-12-09 21:17:30.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- news
tags: []
meta:
  tumblr_erjosito_permalink: http://erjosito.tumblr.com/post/134876076427/acin9k-news-12-is-here
  tumblr_erjosito_id: '134876076427'
  _wp_old_slug: '134876076427'
author:
  login: erjosito
  email: erjosito@hotmail.com
  display_name: erjosito
  first_name: ''
  last_name: ''
permalink: "/2015/12/09/acin9k-news-12-is-here/"
---
Hi there,

sorry for the delay in this edition, but I wanted to wait until ACI 1.2 hits the road. I had some early releases in my lab and I have to tell you: it is AWESOME. But donÔÇÖt let me bore you, here you have my summary of past month until today:

- Happy birthday on the 2-year anniversary from ACI announcement in NYC on Nov 6th 2013! Time has flown ever since!
- New software:
  - ACI 1.2(1i) is there!! With this version Cisco has delivered yet another ACI milestone (the 3rd ÔÇ£majorÔÇØ release in 6 quarters).
    - Here the APIC release notes: [http://www.cisco.com/c/en/us/td/docs/switches/datacenter/aci/apic/sw/1-x/release/notes/apic\_rn\_121i.html](http://www.cisco.com/c/en/us/td/docs/switches/datacenter/aci/apic/sw/1-x/release/notes/apic_rn_121i.html)
    - LOTS and LOTS of new great functions! Here my favourites:
      - Basic, simplified GUI (the ÔÇ£advanced GUIÔÇØ stays, the user can toggle between basic and advanced)
      - NXOS-style CLI for ACI: back in black!
      - VM-EPG assignment based on VM attributes for Hyper-V too (it was already possible in vSphere)
      - IP-based EPG assignment for virtual or physical workloads
      - Unmanaged service graphs: automate service insertion even if no device package is available
      - And many more. Check out the release notes (link above) for more!
    - Citrix has released a new version of the device package: [https://www.citrix.com/blogs/2015/11/10/new-netscaler-device-package-for-cisco-aci/](https://www.citrix.com/blogs/2015/11/10/new-netscaler-device-package-for-cisco-aci/)
    - Camden MR aka 7.0(3)I2(2) is in CCO ready to download, more info in the release notes: [http://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus9000/sw/6-x/release/notes/70322\_nxos\_rn.html](http://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus9000/sw/6-x/release/notes/70322_nxos_rn.html)
    - VTS 2.0 is now GA! For more info go to [http://www.cisco.com/go/vts.](http://www.cisco.com/go/vts.) Ordering guide here: [http://www.cisco.com/c/en/us/products/collateral/cloud-systems-management/virtual-topology-system/guide-c07-736079.html](http://www.cisco.com/c/en/us/products/collateral/cloud-systems-management/virtual-topology-system/guide-c07-736079.html) (including pricing).
    - Nexus Data Broker 2.2 released! More info in the release notes:
      - [http://www.cisco.com/c/en/us/td/docs/net\_mgmt/xnc/nexus\_data\_broker/release\_notes/nexus\_data\_broker\_release\_notes\_22.html](http://www.cisco.com/c/en/us/td/docs/net_mgmt/xnc/nexus_data_broker/release_notes/nexus_data_broker_release_notes_22.html)
    - UCS 5.4 is out! Support for ACI 1.1(3) is there! The new UCSD 5.4 APIC Mgmt Guide is here: [http://www.cisco.com/c/en/us/td/docs/unified\_computing/ucs/ucs-director/apic-management-guide/5-4/b\_UCS\_Director\_APIC\_Management\_Guide\_54.html.](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/ucs-director/apic-management-guide/5-4/b_UCS_Director_APIC_Management_Guide_54.html.)
      - The one feature I canÔÇÖt wait to try is the ÔÇ£Tenant onboarding with private networksÔÇØ. That is, no need for UCS or VNX/Netapp any more to use the native ACI integration, you just need a VMware and an ACI.
  - Some press on Cisco ACI:
    - LightReading about the new 1.2 release: [http://www.lightreading.com/carrier-sdn/sdn-technology/cisco-enhances-sdn-capabilities-of-aci/d/d-id/719654](http://www.lightreading.com/carrier-sdn/sdn-technology/cisco-enhances-sdn-capabilities-of-aci/d/d-id/719654)
    - Great report on ACI: ÔÇ£ACI shakes up SDN": [http://www.infoworld.com/article/3003903/sdn/review-cisco-aci-application-centric-infrastructure-shakes-up-sdn.html](http://www.infoworld.com/article/3003903/sdn/review-cisco-aci-application-centric-infrastructure-shakes-up-sdn.html)
    - ACI as Top Trend for 2016: [http://www.infoworld.com/article/3007057/cloud-computing/9-enterprise-tech-trends-for-2016-and-beyond.html](http://www.infoworld.com/article/3007057/cloud-computing/9-enterprise-tech-trends-for-2016-and-beyond.html)
    - Cisco ACI awarded InfoWorld EditorÔÇÖs Choice: [http://blogs.cisco.com/partner/cisco-aci-awarded-infoworld-editors-choice](http://blogs.cisco.com/partner/cisco-aci-awarded-infoworld-editors-choice)
    - Reflections on ACI: [https://www.sdxcentral.com/articles/featured/aci-cisco-article/2015/11/](https://www.sdxcentral.com/articles/featured/aci-cisco-article/2015/11/)
    - Nexus 9K+3K at $2B run rate! ([http://www.crn.com/news/networking/300078813/ciscos-q1-success-overshadowed-by-bleak-q2-outlook.htm](http://www.crn.com/news/networking/300078813/ciscos-q1-success-overshadowed-by-bleak-q2-outlook.htm))
  - ACI is now included in the CCIE DC v2.0 blueprint: [http://www.cisco.com/web/learning/certifications/shared/docs/ccie-datacenter-comparison.pdf](http://www.cisco.com/web/learning/certifications/shared/docs/ccie-datacenter-comparison.pdf)
  - On Openstack:
    - A blog entry by Lauren on ACIÔÇÖs latest innovations regarding Openstack: [http://blogs.cisco.com/datacenter/cisco-aci-at-openstack-2015-in-tokyo](http://blogs.cisco.com/datacenter/cisco-aci-at-openstack-2015-in-tokyo)
    - ACI+Openstack GBP+OneConvergence (5:37): [https://www.youtube.com/watch?v=UYmM\_uBtu2s](https://www.youtube.com/watch?v=UYmM_uBtu2s)
    - Tech Field Day, Lucien on ACI & Openstack (15:54): [https://www.youtube.com/watch?v=3QFz2iqrvvk](https://www.youtube.com/watch?v=3QFz2iqrvvk)
    - Lucien strikes again, with an intro on GBP (3:34): [https://www.youtube.com/watch?v=r0dGIxpqzf4](https://www.youtube.com/watch?v=r0dGIxpqzf4)
    - GBP with Openstack (2:58): [https://www.youtube.com/watch?v=jF4aK1b383gACI](https://www.youtube.com/watch?v=jF4aK1b383gACI)
    - Openstack GBP and Opflex (15:17): [https://www.youtube.com/watch?v=3jSgVXxQRBo](https://www.youtube.com/watch?v=3jSgVXxQRBo)
  - On Linux containers:
    - ACI integration with Docker (8:46): [https://www.youtube.com/watch?v=La7fiE00slQ](https://www.youtube.com/watch?v=La7fiE00slQ)
    - A blog entry on containers with a link for ebook download: [http://blogs.cisco.com/cloud/why-containerize](http://blogs.cisco.com/cloud/why-containerize)
    - DonÔÇÖt miss BalajiÔÇÖs blog on Contiv: [http://blogs.cisco.com/datacenter/project-contiv](http://blogs.cisco.com/datacenter/project-contiv)
    - Apprenda and ACI (4:21): [https://www.youtube.com/watch?v=\_GAg\_\_YfqUg](https://www.youtube.com/watch?v=_GAg__YfqUg)
    - Docker 1.9 is out with lots of enhancements on networking: [http://blog.docker.com/2015/11/docker-multi-host-networking-ga/](http://blog.docker.com/2015/11/docker-multi-host-networking-ga/)
    - More on containers: in the last Openstack summit we announced Mantl 0.4: [http://blogs.cisco.com/cloud/microservices-infrastructure-mantl-release-0-4](http://blogs.cisco.com/cloud/microservices-infrastructure-mantl-release-0-4)
    - Once again, about Docker integration (15:53): [https://www.youtube.com/watch?v=SsY2-E0D98g](https://www.youtube.com/watch?v=SsY2-E0D98g)
  - Some technical notes:
    - New TechNote for ACI on configuring VMM integration on UCS: [http://www.cisco.com/c/en/us/support/docs/cloud-systems-management/application-policy-infrastructure-controller-apic/118965-config-vmm-aci-ucs-00.html](http://www.cisco.com/c/en/us/support/docs/cloud-systems-management/application-policy-infrastructure-controller-apic/118965-config-vmm-aci-ucs-00.html)
    - A note on mgmt networks in ACI: ÔÇ£If you are not using INB (inband management), the fabric will default to OOB (out of band management) for things like NTP, AAA, and others. If you have an operable INB setup, then it would be only used for redundancy. INB, when configured, is preferred over OOB.ÔÇ£
    - When setting the ÔÇ£preferredÔÇØ checkbox for a subnet in an ACI BD, ACI will relay DHCP requests in that BD from that subnet.
    - ACI NTP config and demo (13:05): [https://www.youtube.com/watch?v=yfeQ\_mokxYI](https://www.youtube.com/watch?v=yfeQ_mokxYI)
  - From the web:
    - VCE supports now ÔÇ£powered-by-ACIÔÇØ Vblocks: [http://newsroom.cisco.com/press-release-content?type=webcontent&articleId=1730531](http://newsroom.cisco.com/press-release-content?type=webcontent&articleId=1730531)
    - DuÔÇÖs Case Study: [http://www.cisco.com/web/about/success-stories/docs/du.html](http://www.cisco.com/web/about/success-stories/docs/du.html)
    - DemoFriday on switch deployment with Ansible: [https://www.sdxcentral.com/resources/sdn-demofriday/open-nx-os-cisco-demofriday-devops/](https://www.sdxcentral.com/resources/sdn-demofriday/open-nx-os-cisco-demofriday-devops/)
    - Application Network Profiler (ANP) tool: [http://www.cisco.com/c/dam/en/us/solutions/collateral/data-center-virtualization/application-centric-infrastructure/white-paper-c11-735428.pdf](http://www.cisco.com/c/dam/en/us/solutions/collateral/data-center-virtualization/application-centric-infrastructure/white-paper-c11-735428.pdf)
    - TechWise on Puppet: [http://www.cisco.com/web/learning/le21/onlineevts/offers/twtv/en/twtv184/ondemand.html](http://www.cisco.com/web/learning/le21/onlineevts/offers/twtv/en/twtv184/ondemand.html)
    - Infographic on ÔÇ£Top 5 ways that ACI makes IT agileÔÇØ: [http://www.cisco.com/c/dam/m/en\_us/solutions/data-center/offers/efficiency/dc-08-dc-cloud-top5s-cte-en.pdf](http://www.cisco.com/c/dam/m/en_us/solutions/data-center/offers/efficiency/dc-08-dc-cloud-top5s-cte-en.pdf)
  - Blogs
    - Soni Jiandani: [http://blogs.cisco.com/news/executing-on-our-vision-and-strategy-the-future-of-networking-for-an-application-centric-world](http://blogs.cisco.com/news/executing-on-our-vision-and-strategy-the-future-of-networking-for-an-application-centric-world)
    - Must Knows on Devops, Open NXOS, Ansible: [http://blogs.cisco.com/datacenter/ansiblefest2015](http://blogs.cisco.com/datacenter/ansiblefest2015)
    - ACI DC innovations: [http://blogs.cisco.com/datacenter/aci-datacenter-innovations-top-3-ways](http://blogs.cisco.com/datacenter/aci-datacenter-innovations-top-3-ways)
    - Blog by Praveen on ACI from an automation and operation perspective: [http://blogs.cisco.com/datacenter/aci-the-sdn-purpose-built-for-data-center-operations-and-automation](http://blogs.cisco.com/datacenter/aci-the-sdn-purpose-built-for-data-center-operations-and-automation)
  - Videos:
    - New ACI NXOS-based CLI: [https://youtu.be/It4pddw5\_es](https://youtu.be/It4pddw5_es)  
Splunk and ACI (3:51): [https://www.youtube.com/watch?v=ZHWTibFJZEw](https://www.youtube.com/watch?v=ZHWTibFJZEw)
    - ACI dual DC innovations, including multisite toolkit app and cross-vCenter vMotion (31:34): [https://www.youtube.com/watch?v=RFBX65GZYNs](https://www.youtube.com/watch?v=RFBX65GZYNs)
    - ScienceLogic: ACI monitoring demo (4:22): [https://www.youtube.com/watch?v=fXnH6FjAg4Y](https://www.youtube.com/watch?v=fXnH6FjAg4Y)
    - A10 and ACI integration (6:52): [https://www.youtube.com/watch?v=02EAONJdGJM](https://www.youtube.com/watch?v=02EAONJdGJM)
    - Great video by Santiago on ASA clusters over 2 ACI fabrics (19:53): [https://www.youtube.com/watch?v=Qn5Ki5SviEA](https://www.youtube.com/watch?v=Qn5Ki5SviEA)
    - TechWise TV: Open NXOS and Puppet enable DevOps (25:46): [https://www.youtube.com/watch?v=f7\_M1MCxzxg](https://www.youtube.com/watch?v=f7_M1MCxzxg)
    - ACI-F5 integration demo: [https://www.youtube.com/watch?v=7jXTS6COKss](https://www.youtube.com/watch?v=7jXTS6COKss) (includes BigIP, BigIQ and unmanaged)
    - ASA and FirePower in ACI (32:32): [https://www.youtube.com/watch?v=rcGoZUx4nNs](https://www.youtube.com/watch?v=rcGoZUx4nNs)
    - ACI for the MSFT cloud (1:05): [https://www.youtube.com/watch?v=YlF1eXlPqQ0](https://www.youtube.com/watch?v=YlF1eXlPqQ0)

Thanks for reading down to here!

[http://erjosito.tumblr.com/ask](http://erjosito.tumblr.com/ask)

