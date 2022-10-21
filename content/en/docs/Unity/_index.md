---
title: "Unity"
linkTitle: "Unity"
weight: 6
date: 2017-01-05
---

Unity is a legal practice management software solution built on the GSF platform. It enables efficient management and execution of different types of legal matters.

{{< figure src="unity_conceptual_overview.drawio.svg" caption="Unity Conceptual Overview">}}

Key concepts include:

* __Guided workflow__: capture and automate business processes so the software can direct users towards the correct activities, as well as providing provide context and instruction for how to execute these activities
* __Document driven__: precedents drive the required data collection and workflow tasks required to collect that data
* __Supports multiple areas of law__: Unity will support a set of common features that apply to all areas of law, plus the ability to add area of law specific functionality. An area of law is defined by:
  * __Matter Data__: Each area of law will have its own schema with an API for storing and accessing the data
  * __Workflow__: Each area of law will have a different workflow composed of common and specific workflow tasks
  * __Precedents__: Each area of law will use a combination of common and specific precedents

```plantuml
@startuml system_landscape_unity
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

title Unity System Landscape

Person(person_user, "Our Customer")
Person(person_occ, "Our Customer's Customer")
Person(person_precedent_admin, "Precedent Admin")


System_Boundary(system_unity, "Unity"){
   Container(container_matters_ui, "Matters UI", "Flutter microsite", "Main user interface for Unity")

   Container(container_precedents_word_addin, "precedents_word", "Microsoft Office Add-in- Flutter Microsite", "Addin for Microsoft Word allowing users to code precedents for merging via Unity")
   
   System_Boundary(subsystem_matter_data_apis, "Matter Data APIs"){
    Container(container_gsf_matters, "GSF.Matters", "C# microservice", "Generic storage for matter data across all areas of law")
    Container(container_gsf_contacts, "GSF.Contacts", "C# microservice", "Storage of  matter data across all areas of law")
    Container(container_gsf_matters_leasing, "GSF.Matters.Leasing", "C# microservice", "Area of Law specific API to manage leasing matters")
    Container(container_gsf_matters_conveyancing, "GSF.Matters.Conveyancing", "C# microservice", "Area of Law specific API to manage Conveyancing matters")
    Container(container_gsf_matters_conveyancing_projects, "GSF.Matters.Conveyancing.Projects", "C# microservice", "Area of Law specific API to manage Conveyancing Projects matters")
   }

   Container(container_gsf_workflow, "GSF.Workflow", "C# microservice", "Service for managing workflows")


   Container(container_gsf_document_store, "GSF.Document.Store", "C# microservice", "Storage for documents associated with a matter")

   Container(container_gsf_documentassembly, "GSF.Provider.DocumentAssembly", "C# microservice", "Storage and search for precedents (document templates)")

   Container(container_gsf_document_generate, "GSF.Document.Generate", "Python microservice", "Generates documents from templates")

   Container(container_auth_attributes, "Authorization.Attributes", "C# microservice", "Aggregates matter data for the Policy Agent to make decisions")

   Container(container_opa, "Open Policy Agent", "", "Evaluates user access to resources based on policies")


}

System(system_search, "Search Manager")

System_Boundary(system_gsf_core, "GSF Core"){
   System(gsf_core_auth, "Authentication & Authorisation", "")
   System(gsf_core_documents, "Document Management", "")
   System(gsf_core_orders, "Order Management", "")
   System(gsf_core_users, "User & Account management", "")
   System(gsf_core_billing, "Billing", "")
   System(gsf_core_products, "Product Management", "")
}  

Rel(person_user, container_matters_ui, "uses", "")
Rel(person_occ, container_matters_ui, "uses", "")
Rel(person_precedent_admin, container_precedents_word_addin, "users codes precedents", "")

Rel(container_precedents_word_addin, container_gsf_documentassembly, "precedents saved to precedent store", "")


Rel(container_matters_ui, subsystem_matter_data_apis, "User can view/edit matter data through UI", "")
Rel(container_matters_ui, container_gsf_workflow, "User can interact with workflow through UI", "")
Rel(container_matters_ui, container_gsf_document_store, "User can retrieve documents through UI", "")

Rel(container_gsf_workflow, subsystem_matter_data_apis, "workflow reads matter data", "")
Rel(container_gsf_workflow, container_gsf_documentassembly, "workflow reads precedents", "")
Rel(container_gsf_workflow, container_gsf_document_generate, "Workflow generates documents from precedents and matter data", "")
Rel(container_gsf_workflow, container_gsf_document_store, "Workflow saves generated documents", "")

Rel(container_auth_attributes, subsystem_matter_data_apis, "reads matter data", "")
Rel(container_opa, container_auth_attributes, "reads matter data", "")
Rel(subsystem_matter_data_apis, container_opa, "uses OPA to verify user access", "")





Rel(system_unity, system_search, "users purchase search products in workflow context from", "")
Rel(system_unity, system_gsf_core, "uses", "")




@enduml
```
