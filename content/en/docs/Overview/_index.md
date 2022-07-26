---
title: "Overview"
linkTitle: "Overview"
weight: 1
---

## What is GSF?

GlobalX Services Framework (GSF) is the set of patterns, services and applications for providing software features to external and internal customers. 

{{< figure src="gsf2_conceptual_overview.drawio.svg" caption="GSF Conceptual Overview">}}

It provides:
* the underlying [platform]({{< relref "../GSF Platform/" >}}), which includes:
  * [infrastructure]({{< relref "../GSF Platform/1 Patterns/GCP Infrastructure.md" >}}) on which the services are hosted
  * platform services for generic concerns such as [traffic management]({{< relref "../GSF Platform/1 Patterns/Request Flow.md" >}}), data storage and [observability]({{< relref "../GSF Platform/1 Patterns/Monitoring.md" >}})
  * patterns and tools for [CI/CD]({{< relref "../GSF Platform/1 Patterns/CI_CD.md" >}})
* a set of [core services]({{< relref "../GSF Core Services/" >}}) providing common application concerns such as management of users, products and documents
* applications that consume and build on the platform and core services such as the [Search platform]({{< relref "../Search/" >}}) and [Unity]({{< relref "../Unity/" >}})

### Cloud Native

GSF is cloud-native and is deployed entirely on Google Cloud Platform. It is intended to be cloud-agnostic for future flexibility, achieved by favouring kubernetes native components over cloud vendor specific solutions.

### Microservices Architecture

GSF is based on a [Microservice architecture]({{< relref "../GSF Platform/3 Components/Microservice.md" >}}), providing HTTP APIs which are consumed by [Microsites]({{< relref "../GSF Platform/3 Components/Microsite.md" >}}) (browser based SPAs) and integrated directly into customer applications. Microservices communicate internally via HTTP and AMQP.

{{< figure src="gsf2_architecture_overview.drawio.svg" caption="GSF Architecture Overview">}}
