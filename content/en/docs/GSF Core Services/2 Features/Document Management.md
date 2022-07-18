---
title: "Document Management"
linkTitle: "Document Management"
weight: 2
description: >
  Document Management
---

## Introduction

```plantuml
@startuml gsf_core_auth
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

title GSF Core Order Management Container Diagram

Person(person_user, "Our Customer")
System(search_app, "Search application")
System(unity_app, "Unity application")
System(customer_app, "Customer application")



System_Boundary(c1, "Document Management"){
    Container(intellimanager, "GlobalX.Intellimanager", "C# Monolith", "")

    System_Boundary(system_document_storage, "Intellimanager Document Storage"){
      ContainerDb(db, "Intellimanager_db", "MS SQL Server Database", "")
      ContainerDb(bucket, "intellimanager-bucket", "GCP bucket", "Blob storage for files")
    }


    Container(gsf_pdf, "GSF.PDF", "C# Microservice", "")

    Container(gsf_documentstore, "GSF.Document.Store", "C# Microservice", "")
}

Container(GSF_OrderManager_UI, "GSF.OrderManager.UI", "Angular SPA", "")


Rel(intellimanager, system_document_storage, "Access documents", "")

Rel(gsf_documentstore, system_document_storage, "Access documents", "")

Rel(customer_app, gsf_documentstore, "access document APIs", "")

Rel(search_app, intellimanager, "Save user documents", "")
Rel(unity_app, intellimanager, "Save user documents", "")

Rel(intellimanager, gsf_pdf, "Converts documents to PDF", "")

Rel(GSF_OrderManager_UI, gsf_documentstore, "Access user documents", "")
Rel(person_user, GSF_OrderManager_UI, "Views personal documents", "")
Rel(person_user, intellimanager, "Views personal documents", "")


@enduml
```

## Document UIs

There are a number of UIs for viewing documents:
* Order Manager UI: allows customers to view the documents saved against each of their orders
* OFM UI: allows internal staff to view customer orders and documents
* IntelliDoc: The UI component of the Intellimanager application is referred to as IntelliDoc. It is deprecated in favour or Order Manager but still fully functional

## Document APIs

The latest Document Store API is GSF.Document.Store. Older APIs are still in use but deprecated e.g. GSF.Documents.Endpoint V1 & V2. 

GSF.Document.Store is the recommended mechanism for customer applications to retrieve and store user documents.

## Saving Documents

There are 2 ways for an application to save a document for a user:
* RabbitMQ: Publishing a DocumentComposedEvent on the message bus. This message will be handled by Intellimanager which will store the document data and metadata. This is appropriate for internal application backends, especially where there is no active user session/auth token available.
  * Example: Product microservices publish rabbit messages after retrieving data from an external vendor
* HTTP API: POSTing the document to the Document Store API. The Document Store API will store the document data and metadata. This is appropriate for customer applications and internal applications that do not have access to the bus e.g. SPAs, application backend with no RabbitMQ client library available.
  * Example: OFM and Order allows internal staff to upload documents directly

## Document File Format Conversion

GSF.PDF microservice provides functinoality for converting most types of documents to PDF, either synchronously or asynchronously. More detail [here](https://bitbucket.globalx.com.au/projects/GSF/repos/gsf.pdf/browse).

## Document access business rules

The document management system provides the ability to control sharing of documents between users in an organisation, based on the organisation and user settings. This functionality is described in detail [here](https://dyedurhamau.atlassian.net/wiki/spaces/GP/pages/2357744181/intelli-Doc+Sharing).