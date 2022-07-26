---
title: "Unity"
linkTitle: "Unity"
weight: 6
date: 2017-01-05
---

Unity is a legal practice management software solution built on the GSF platform. It enables efficient management and execution of different types of legal matters.

{{< figure src="unity_conceptual_overview.drawio.svg" caption="Unity Conceptual Overview">}}

Key concepts include:

* __Guided workflow__: business processes captured in the software to direct users towards the correct activities based on the current state of the matter, provide context, instruction and validation on how to execute the next step in the workflow
* __Document driven__: precedents drive the required data collection and workflow tasks required to collect that data
* __Supports multiple areas of law__: Different areas of law can be built over the top of core Unity features. Each area of law will have pre-defined configurations (e.g. workflows and precedents), with customers having the ability to customise workflows and precedents to account for their own requirements.

An area of law is defined by:
* __Matter Data__: Each area of law will have its own schema with an API for storing and accessing the data
* __Workflow__: Each area of law will have a different workflow composed of common and specific workflow tasks
* __Precedents__: Each area of law will use a combination of common and specific precedents

```plantuml
@startuml system_landscape_unity
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

title GSF Core Service System Landscape

Person(person_user, "Our Customer")
Person(person_occ, "Our Customer's Customer")

System_Boundary(system_unity, "Unity"){
   Container(container_matters_ui, "Matters UI", "Flutter microsite", "Main user interface for Unity")
   Container(container_gsf_matters, "GSF.Matters", "C# microservice", "Generic storage for matter data across all areas of law")
   Container(container_gsf_contacts, "GSF.Contacts", "C# microservice", "Storage of  matter data across all areas of law")
   Container(container_gsf_matters_leasing, "GSF.Matters.Leasing", "C# microservice", "Area of Law specific API to manage leasing matters")
   Container(container_gsf_matters_conveyancing, "GSF.Matters.Conveyancing", "C# microservice", "Area of Law specific API to manage Conveyancing matters")
   Container(container_gsf_matters_conveyancing, "GSF.Matters.Conveyancing.Projects", "C# microservice", "Area of Law specific API to manage Conveyancing Projects matters")

   Container(container_gsf_contacts, "GSF.Contacts", "C# microservice", "Generic service for creating and retrieving contacts")
   Container(container_gsf_workflow, "GSF.Workflow", "C# microservice", "Service for managing workflows")
   Container(container_precedents_word_addin, "precedents_word", "Microsoft Office Add-in- Flutter Microsite", "Addin for Microsoft Word allowing users to code precedents for merging via Unity")

}

System(system_search, "Search Manager")

System_Boundary(system_gsf_core, "GSF Core"){
   System(gsf_core_auth, "Auth", "Auth Services")
   System(gsf_core_documents, "Document Management", "Document Management and Storage Services")
   System(gsf_core_billing, "Billing Management", "Billing Services")
}  


Rel(system_unity, system_search, "users purchase search products in workflow context from", "")
Rel(system_unity, system_gsf_core, "uses", "")
Rel(person_user, container_matters_ui, "uses", "")
Rel(person_occ, container_matters_ui, "uses", "")


@enduml
```

<!-- 

```plantuml
@startuml container_unity
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

Person(person_lawyer, "Our Customer")

System_Boundary(c1, "Unity"){
    Container(container_matters_ui, "Matters UI", "Flutter microsite", "Main user interface for Unity")
    Container(container_gsf_matters, "GSF.Matters", "C# microservice", "Generic storage for matter data across all areas of law")
    Container(container_gsf_matters_leasing, "GSF.Matters.Leasing", "C# microservice", "An API to manage leasing matters")
    Container(container_gsf_matters_conveyancing, "GSF.Matters.Conveyancing", "C# microservice", "An API to manage Conveyancing matters")
    Container(container_gsf_contacts, "GSF.Contacts", "C# microservice", "Generic service for creating and retrieving contacts")
    Container(container_gsf_workflow, "GSF.Workflow", "C# microservice", "Service for managing workflows")
    Container(container_gsf_workflow_configuration, "GSF.WorkflowConfiguration", "C# microservice", "Storage of shared configurations between services and applications")
    Container(container_gsf_workflow_agent, "GSF.Workflow.Agent", "C# microservice", "A service which handles messages relating to the gsf.workflow")
    Container(container_wkctl, wkctl, "CLI application", "Publishes a workflow template from a yaml file to GSF.Workflow as a template for workflow instances.")
    Container(container_precedents_word_addin, "precedents_word", "Microsoft Office Add-in- Flutter Microsite", "Addin for Microsoft Word allowing users to code precedents for merging via Unity")

    Container_Boundary(container_docassemble, "Docassemble") {
        Component(component_docassemble_globalx_common, "docassemble.globalx.common", "docassemble", "Common docassemble package that contains common functions to be used by different areas of law")
        Component(component_docassemble_globalx_leasing, "docassemble.globalx.leasing", "docassemble", "Docassemble package that contains leasing interviews and service classes")
        Component(component_docassemble_global_conveyancing, "docassemble.globalx.conveyancing", "docassemble", "Docassemble package that contains conveyancing interviews and service classes")
    }
}

System_Boundary(c2, "GSF"){
    Container(container_gsf_notes, "GSF.Notes", "C# microservice", "Manage user notes")
    Container(container_gsf_documents_endpoint, "GSF.Documents.Endpoint.V2", "C# microservice", "Documents management")
    Container(container_gsf_document_assembly, "GSF.Provider.DocumentAssembly", "C# microservice", "Storage of Precedent documents (also provides Document Assembly via XpressDocs for OP and existing projects portal but not for Unity")
}

Rel(person_lawyer, container_matters_ui, "", "")

Rel(container_matters_ui, container_gsf_matters, "", "")
Rel(container_matters_ui, container_gsf_matters_leasing, "", "")
Rel(container_matters_ui, container_gsf_matters_conveyancing, "", "")
Rel(container_matters_ui, container_gsf_contacts, "", "")
Rel(container_matters_ui, container_gsf_workflow, "", "")
Rel(container_matters_ui, container_gsf_workflow_configuration, "", "")
Rel(container_matters_ui, container_gsf_workflow_agent, "", "")
Rel(container_matters_ui, container_wkctl, "", "")
Rel(container_matters_ui, container_precedents_word_addin, "", "")

Rel(container_matters_ui, container_gsf_notes, "", "")
Rel(container_matters_ui, container_gsf_documents_endpoint, "", "")
Rel(container_matters_ui, container_gsf_document_assembly, "", "")

Rel(container_matters_ui, container_docassemble, "", "")

@enduml
``` -->
