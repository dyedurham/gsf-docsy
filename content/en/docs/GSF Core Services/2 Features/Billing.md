---
title: "Billing"
linkTitle: "Billing"
weight: 2
description: >
  Billing
---

## Introduction

GSF Core Billing provides UIs and APIs for users and applications to submit and view billing transactions.

GSF does not directly handle any customer payment proceessing. Instead, applications notify the GSF Core Billing system that a billable action has occurred. Billing transactions are stored in gxsdb and synced to the finance system which is used by the finance team to generate invoices and process payments from customers.

Note that while Billing is included as a GSF Core service, it does have coupling to Search Application in that a it requires Search User and Products to be created in gxsdb.

```plantuml
@startuml gsf_core_billing
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

title GSF Core Billing Container Diagram

Person(person_user, "Our Customer")
Person(person_internal_user, "Internal Staff")

System(search_portal, "Search Portal")
System(customer_app, "Customer Applications")

System_Boundary(system_billing, "Billing"){
    Container(gsf_billing_report_ui, "gsf.billing-report.ui", "Angular Microsite", "UI for viewing billing transactions")
    Container(GSF_Billing, "GSF.Billing", "C# Microservice", "TODO")
    Container(GSF_Provider_Product, "GSF.Provider.Product", "C# Microservice", "TODO")
    Container(GSF_Transactions_Service, "GSF.Transactions.Service", "C# Microservice", "HTTP API for retrieving transaction data")
}

ContainerDb(gxsdb, "GXSDB", "Postgres Database", "Stores user, pricing & transaction data")
Container(gxs_app_server, "gxs-app-server", "Containerised C application server", "Legacy C apps 'Purple pages', including FindTrans")

System_Boundary(system_invoicing, "Invoicing"){
    Container(gsf_au_navision_connector, "GSF.AU.Navision.Connector", "C# Microservice", "Syncs transactions from GSF into Navision")
    Container(gsf_au_navision_invoice_posting, "GSF.AU.Navision.Invoice-Posting", "C# Microservice", "Builds invoices from transactions")
    Container(navision, "Navision", "Application", "Used by finance team for customer invoicing")
}

Rel(search_portal, GSF_Billing, "Generates billing transactions for users", "")
Rel(customer_app, GSF_Transactions_Service, "Retrieve billing transactions for display and dispersement", "")
Rel(GSF_Transactions_Service, gxsdb, "Retrieve billing transactions", "")
Rel(GSF_Billing, gxsdb, "writes GSF transactions", "")
Rel(person_user, search_portal, "Purchase products and services", "")
Rel(person_user, gsf_billing_report_ui, "Views transactions", "")
Rel(gsf_billing_report_ui, GSF_Transactions_Service, "retrieves transactions", "")
Rel(person_internal_user, gxs_app_server, "Views customer transactions on FindTrans", "")
Rel(gxs_app_server, gxsdb, "retrieves transactions for FindTrans UI", "")
Rel(gsf_au_navision_connector, gxsdb, "reads GSF transactions", "")
Rel(gsf_au_navision_connector, navision, "creates transactions", "")
Rel(gsf_au_navision_invoice_posting, gxsdb, "reads GSF transactions", "")
Rel(gsf_au_navision_invoice_posting, navision, "creates invoices", "")

@enduml
```

Note: GSF.Transactions.Service provides HTTP API for accessing transaction data. GSF.TransactionBilling.Endpoint is a related API that provides older versions of transaction data. This API is still in use but is now deprecated and omitted from the diagram.

## How to Bill a user

Applications can use GSF.Billing service to create billing transactions. GSF.Billing writes transactions to Gxsdb. This can be triggered by:
* publishing a ReadyForBillingEvent onto the RabbitMQ message bus
* [HTTP API call](https://bitbucket.globalx.com.au/projects/GSF/repos/gsf.billing/browse/src/GSF.Billing.EndPoint/NancyModules/V2/TransactionsModule.cs), available internally at http://billing-endpoint.gsf.production.globalx.com.au:8054/v2/transactions


## Pricing

TODO
See https://dyedurhamau.atlassian.net/wiki/spaces/GP/pages/2357742332/GlobalX+Billing