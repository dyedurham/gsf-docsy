---
title: "GSF Platform"
linkTitle: "GSF Platform"
weight: 2
description: >
  Information about the GSF platform
---

{{< figure src="gsf_platform_conceptual_overview.drawio.svg">}}

The GSF Platform provides:
* the underlying [_infrastructure_]({{< relref "../GSF Platform/1 Patterns/GCP Infrastructure.md" >}}) on which the services are hosted, and tooling for provisioning this infrastructure
* _Platform Services_  providing common concerns such as [traffic management]({{< relref "../GSF Platform/1 Patterns/Request Flow.md" >}}), data storage and [observability]({{< relref "../GSF Platform/1 Patterns/Monitoring.md" >}})
* patterns and tools for [_CI/CD_]({{< relref "../GSF Platform/1 Patterns/CI_CD.md" >}})

Key facts:

* The GSF platform is hosted in [Google Cloud](https://console.cloud.google.com/).

* There are 3 AU environments- [Development](https://console.cloud.google.com/home/dashboard?project=glx-development-au), [Staging](https://console.cloud.google.com/home/dashboard?project=glx-staging-au), [Production](https://console.cloud.google.com/home/dashboard?project=glx-production-au)- each with their own GCP Projects. The goal is to have all environments mirrored as much as possible.

* There are broadly 2 categories of GSF applications based on how they are hosted:
  * Kubernetes hosted applications, running in Linux based docker containers
  * Windows hosted applications, installed directly onto Windows virtual machines

* New application development is done in the Kubernetes pattern. Windows hosted applications are being migrated over to the Kubernetes pattern.
