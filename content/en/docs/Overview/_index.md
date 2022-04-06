---
title: "Overview"
linkTitle: "Overview"
weight: 1
---

# C4 System Landscape Diagram

```plantuml
@startuml system_landscape
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

Person(person_lawyer, "Our Customer", "A staff member of a firm that uses GlobalX products e.g. Lawyer, Paralegel, Solicitor")
Person(person_occ, "Our Customer's Customer", "Someone who is a customer of a GlobalX customer e.g. a Property Developer, Real Estate Agent, Lessor")

System(system_gsf, "GSF", "GSF core system including authentication, user management, document delivery, order management")
System(system_search, "Search", "Services for procuring and creating legal information and documents")
System(system_vnext, "VNext", "Software System that provides core Vnext functionality")
System(system_clss, "CLSS", "Provides conveyancing, lodging, settlements and stamping services")
System(system_terrain, "Open Practice", "")
System(system_matter_centre, "Matter Centre", "")
System(system_terrain, "Terrain", "")

Rel(person_lawyer, system_search, "Our customer procures data and documents via Search services")
Rel(person_lawyer, system_vnext, "Our customer uses VNext for productivity")
Rel(person_lawyer, system_matter_centre, "")
Rel(person_lawyer, system_clss, "")
Rel(person_lawyer, system_terrain, "")
Rel(system_vnext, system_search, "VNext uses Search services as part of automating workflow")
Rel(system_vnext, system_gsf, "VNext consumes GSF services")
Rel(system_search, system_gsf, "Search consumes GSF services")
Rel(system_terrain, system_search, "")
Rel(system_matter_centre, system_search, "")
Rel(system_matter_centre, system_gsf, "")

Rel(person_occ, system_vnext, "Our customer can communicates with their customer through VNext OCC functionality")
@enduml
```
