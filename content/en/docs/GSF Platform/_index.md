---
title: "GSF Platform"
linkTitle: "GSF Platform"
weight: 2
date: 2017-01-05
description: >
  Information about the GSF platform
---

Key facts:

* The GSF platform is hosted in Google Cloud.

*There are 3 environments- Development, Staging, Production- each with their own GCP Projects. The goal is to have all environments mirrored as much as possible.

* There are broadly 2 categories of GSF applications based on how they are hosted- Kubernetes hosted applications and Windows applications. All new application development is done in the Kubernetes pattern, while we migrate and maintain the Windows hosted applications.



## Infrastructure Components

This diagram highlights the key Google Cloud infrastructural components that are used for the GSF2 platform.

{{< figure src="gsf2 infrastructure.drawio.svg" caption="Mouse over the above image and click the edit button!">}}

## Deployment Diagram

The following diagrams show how traffic is routed through the platform to the applications. 

The first shows the key components and how they are deployed.

{{< figure src="gsf2 deployment.drawio.svg" caption="Mouse over the above image and click the edit button!">}}


The second shows conceptually how data flows through these components.

```mermaid
graph TD

CF(Cloudflare)
LBIngress("GCP Cloud HTTP Load Balancer <br /> (ingress.production.globalx.com.au)")
IngressWC("Ingress Controller <br /> (Wildcard Ingress)")
Kong{Kong API Gateway}
IngressApp("Ingress Controller <br /> (App Ingress)")

KSvc(K8S Service)
KPod(K8S Pod)
KContainer(K8S Container)
LBHap(GCP Cloud HTTP Load Balancer <br /> ms-proxy.production.globalx.com.au)
HAP(HA Proxy)
MSW(Windows VM <br /> prw-msw-0.production.globalx.com.au)


User --> CF
CF --> LBIngress
LBIngress --> IngressWC
IngressWC --> Kong

Kong -->|If upstream is K8S hosted| IngressApp
IngressApp --> KSvc
KSvc --> KPod
KPod --> KContainer

Kong -->|If upstream is Windows hosted| LBHap
LBHap --> HAP
HAP --> MSW
```

## DevOps Components

TODO: overview of glx-devops-au environment and ci/cd tools