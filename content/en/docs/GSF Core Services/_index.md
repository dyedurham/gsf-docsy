---
title: "GSF Core Services"
linkTitle: "GSF Core Services"
weight: 3
date: 2017-01-05
description: >
  Information about GSF Core Services
---

The GSF Core system is an application layer built upon GSF Platform. It provides core functionality which is reused by other systems and applications. Examples include billing, document handling and order tracking.

## C4 System Landscape Diagram

```plantuml
@startuml system_landscape_gsf_core
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

title GSF Core Service System Landscape

System_Boundary(gsf, "gsf"){
    System_Boundary(c1, "Order Management"){
        Container(GSF_OrderFulfilmentManager_Endpoint, "GSF.OrderFulfilmentManager.Endpoint", "C# Microservice", "Provides APIs for retrieving order status information")
        Container(gsf_orderfulfilmentmanager_ui, "gsf.orderfulfilmentmanager.ui", "Angular SPA", "UI for internal staff to interact with customer orders")
        Container(GSF_OrderManager_UI, "GSF.OrderManager.UI", "Angular SPA", "UI for customers to interact with their own orders")
        Container(GlobalX_IntelliManager, "GlobalX.IntelliManager", "C# Monolith", "Stores orders and documents. Superseded by OFM & Order Manager")
        Container(GlobalX_Order, "GlobalX.Order", "C# Microservice", "API for placing orders into GSF2")
    }

    System_Boundary(c2, "Security & User Account Management"){
        Container(GSF_Common_Security, "GSF.Common.Security", "C# Microservice", "Provides APIs for authentication & session management")
        Container(Keycloak, "Keycloak Auth Server", "Containerised Java App", "auth.globalx.com.au, providing account details and security APIs")
        Container(GSF_UserAdmin_Service, "GSF.UserAdmin.Service", "C# Microservice", "Provides APIs for customers delegated account administration")
        Container(GSF_Users, "GSF.Users", "C# Microservice", "Provides APIs for internal services to access user data")
        Container(GSF_Keycloak_User_Migrator, "GSF.Keycloak.User.Migrator", "C# Microservice", "Syncs user accounts from GXSDB to Keycloak")
    }

    System_Boundary(c3, "Document Storage & Manipulation"){
        Container(GSF_Documents_Endpoint, "GSF.Documents.Endpoint", "C# Microservice", "TODO")
        Container(GSF_Documents_Store, "GSF.Document.Store", "C# Microservice", "TODO")
        Container(GSF_PDF, "GSF.PDF", "C# Microservice", "TODO")
        Container(GSF_Conversion_GIF, "GSF.Conversion.GIF", "C# Microservice", "TODO")
        Container(GSF_Document_Generate, "GSF.Document.Generate", "C# Microservice", "TODO")
    }

    System_Boundary(c4, "Billing & Product Management"){
        Container(GSF_Billing, "GSF.Billing", "C# Microservice", "TODO")
        Container(GSF_Provider_Product, "GSF.Provider.Product", "C# Microservice", "TODO")
        Container(GSF_Transactions_Service, "GSF.Transactions.Service", "C# Microservice", "TODO")
        Container(GSF_TransactionBilling_Endpoint, "GSF.TransactionBilling.Endpoint", "C# Microservice", "TODO")
    }

    System_Boundary(c5, "Miscellaneous services"){
        ContainerDb(GXSDB, "GXSDB", "Postgres Database", "Legacy storage")
        Container(gxs_app_server, "gxs-app-server", "Containerised C application server", "Legacy C apps 'Purple pages'")
        Container(gxs_services_server, "gxs-services-server", "Containerised C application server", "Legacy C apps 'Purple pages'")
        Container(GSF_Branding, "GSF.Branding", "C# Microservice", "TODO")
        Container(GSF_Email, "GSF.Email", "C# Microservice", "TODO")
        Container(GSF_Notes, "GSF.Notes", "C# Microservice", "TODO")
        Container(GSF_Notifications, "GSF.Notifications", "C# Microservice", "TODO")
        Container(GSF_Reports, "GSF.Reports", "C# Microservice", "TODO")
        Container(GSF_Web_Content_Endpoint, "GSF.Web.Content.Endpoint", "C# Microservice", "TODO")
    }
}
System(search_portal, "Search Portal")
System(unity, "Unity")
System(mc, "Matter Centre")
System(customer, "Customer App")

Rel(search_portal, gsf, "uses", "")
Rel(unity, gsf, "uses", "")
Rel(mc, gsf, "uses", "")
Rel(customer, gsf, "uses", "")

@enduml
```
