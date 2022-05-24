---
title: "Overview"
linkTitle: "Overview"
weight: 1
---

## What is GSF?

GlobalX Services Framework (GSF) is the set of services and patterns that are to be used to provide software features to external and internal customers. 

{{< figure src="gsf2_overview.drawio.svg" caption="GSF Overview">}}

It provides:
* the underlying [platform]({{< relref "../GSF Platform/" >}}), which includes:
  * infrastructure on which the services are hosted
  * platform services for generic concerns such as [traffic management]({{< relref "../GSF Platform/1 Patterns/1 Request Flow.md" >}}), data storage and [observability]({{< relref "../GSF Platform/1 Patterns/4 Monitoring.md" >}})
  * patterns and tools for [CI/CD]({{< relref "../GSF Platform/1 Patterns/2 CI_CD.md" >}})
* a set of [core services]({{< relref "../GSF Core Services/" >}}) providing common application concerns such as management of users, products and documents
* applications that consume and build on the platform and core services such as the [Search platform]({{< relref "../Search/" >}}) and [Unity]({{< relref "../Unity/" >}})

GSF is based on a [Microservice architecture]({{< relref "../GSF Platform/3 Components/Microservice.md" >}}), providing HTTP APIs which are consumed by [Microsites]({{< relref "../GSF Platform/3 Components/Microsite.md" >}}) (browser based SPAs) and integrated directly into customer applications.

## C4 System Landscape Diagram

```plantuml
@startuml system_landscape
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

Person(person_lawyer, "Our Customer", "A staff member of a firm that uses GlobalX products e.g. Lawyer, Paralegel, Solicitor")
Person(person_occ, "Our Customer's Customer", "Someone who is a customer of a GlobalX customer e.g. a Property Developer, Real Estate Agent, Lessor")

System(system_gsf, "GSF", "GSF core system including authentication, user management, document delivery, order management")
System(system_search, "Search", "Services for procuring and creating legal information and documents")
System(system_unity, "Unity", "Software System for practice and matter management")
System(system_clss, "CLSS", "Provides conveyancing, lodging, settlements and stamping services")
System(system_terrain, "Open Practice", "")
System(system_matter_centre, "Matter Centre", "")
System(system_terrain, "Terrain", "")

Rel(person_lawyer, system_search, "Our customer procures data and documents via Search services")
Rel(person_lawyer, system_unity, "Our customer uses Unity for productivity")
Rel(person_lawyer, system_matter_centre, "")
Rel(person_lawyer, system_clss, "")
Rel(person_lawyer, system_terrain, "")
Rel(system_unity, system_search, "Unity uses Search services as part of automating workflow")
Rel(system_unity, system_gsf, "Unity consumes GSF services")
Rel(system_search, system_gsf, "Search consumes GSF services")
Rel(system_terrain, system_search, "")
Rel(system_matter_centre, system_search, "")
Rel(system_matter_centre, system_gsf, "")

Rel(person_occ, system_unity, "Our customer can communicates with their customer through Unity OCC functionality")
@enduml
```
