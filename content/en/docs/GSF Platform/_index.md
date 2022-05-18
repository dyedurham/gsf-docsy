---
title: "GSF Platform"
linkTitle: "GSF Platform"
weight: 2
date: 2017-01-05
description: >
  Information about the GSF platform
---

Key facts:

* The GSF platform is hosted in [Google Cloud](https://console.cloud.google.com/).

* There are 3 AU environments- [Development](https://console.cloud.google.com/home/dashboard?project=glx-development-au), [Staging](https://console.cloud.google.com/home/dashboard?project=glx-staging-au), [Production](https://console.cloud.google.com/home/dashboard?project=glx-production-au)- each with their own GCP Projects. The goal is to have all environments mirrored as much as possible.

* There are broadly 2 categories of GSF applications based on how they are hosted:
  * Kubernetes hosted applications, running in Linux based docker containers
  * Windows hosted applications, installed directly onto Windows virtual machines

* New application development is done in the Kubernetes pattern. Windows hosted applications are being migrated over to the Kubernetes pattern.

## Infrastructure Components

This diagram highlights the key Google Cloud infrastructural components that are used for the GSF platform.

{{< figure src="gsf2 infrastructure.drawio.svg" caption="Mouse over the above image and click the edit button!">}}
