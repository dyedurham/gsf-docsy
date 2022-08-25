---
title: "Order Management"
linkTitle: "Order Management"
weight: 3
description: >
  Order Management
---

## Introduction

* Customers acquire data, documents and services by submitting an Order for a Product
* Different types of products are fulfilled by the appropriate Product microservice. These Product microservices publish order status information during the fulfilment process
* Order fulfilment status is tracked by the OFM (Order Fulfilment Manager) microservice (GSF.OrderFulfilmentManager). Users can interact with OFM data through:
  * OFM UI: This application is for use by internal staff, for handling customer enquiries and manually performing fulfilment actions
  * Order Manager UI: This application is for use by customers, to view their orders and the associated documents and billing details


```plantuml
@startuml gsf_core_auth
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

title GSF Core Order Management Container Diagram

Person(person_user, "Our Customer")
Person(person_internal_user, "Internal staff")
System(search_app, "Search application")
System(unity_app, "Unity application")


System_Boundary(c1, "Order Management"){
    Container(GSF_OrderFulfilmentManager_Endpoint, "GSF.OrderFulfilmentManager.Endpoint", "C# Microservice", "Provides APIs for retrieving order status information")
    Container(gsf_orderfulfilmentmanager_ui, "gsf.orderfulfilmentmanager.ui", "Angular Microsite", "UI for internal staff to interact with customer orders")
    Container(GSF_OrderManager_UI, "GSF.OrderManager.UI", "Angular SPA", "UI for customers to interact with their own orders")
}

Rel(person_user, GSF_OrderManager_UI, "Views personal order data", "")
Rel(person_internal_user, gsf_orderfulfilmentmanager_ui, "Views customer order data", "")
Rel(GSF_OrderManager_UI, GSF_OrderFulfilmentManager_Endpoint, "Reads order data from", "")
Rel(gsf_orderfulfilmentmanager_ui, GSF_OrderFulfilmentManager_Endpoint, "Reads order data from", "")

Rel(person_user, search_app, "Acquire products using", "")
Rel(person_user, unity_app, "Acquire products using", "")
Rel(search_app, GSF_OrderFulfilmentManager_Endpoint, "sends order data to", "")
Rel(unity_app, GSF_OrderFulfilmentManager_Endpoint, "sends order data to", "")


@enduml
```

### Anatomy of an Order

An order contains multiple order items. Generally each order item is for one type of [product]({{< relref "./Product Management.md" >}}), stored in the orderType value.  Generally each order item results in the delivery of some [documents]({{< relref "./Document Management.md" >}}) and [billing items]({{< relref "./Billing.md" >}}).

```mermaid

classDiagram
    class Order
    Order : +String orderId
    Order : +OrderItem[] orderItems

    class OrderItem
    OrderItem: +int itemNumber
    OrderItem: +String orderType
    OrderItem: +String payload

    Order "1" --> "*" OrderItem

```

## Order fulfilment flows

### Standard flows

These message flows enable product UIs and integrators to submit orders via HTTP APIs. See also [Provider-Product Pattern]({{< relref "../1 Patterns/Provider Product.md" >}}).

The Synchronous Order Flow is used for ordering products synchronously i.e. the response to the order creation request contains the final data or document. This is used when the vendor makes the data available sycnhronously and the application wishes to display it to the user immediately following order creation.

```plantuml
@startuml

title Synchronous Order Flow

actor User
participant "Product UI" as ui
participant "Product Microservice" as product_api
participant "External Vendor" as vendor
participant "GSF.OrderFulfilmentManager" as ofm
participant "GSF.Billing" as billing
participant "GlobalX.Intellimanager" as intellimanager

User->ui
ui->product_api: HTTP POST
product_api->ofm: FulfilmentRequestReceivedEvent
product_api->vendor: Retrieve data from vendor API
product_api->intellimanager: AMQP DocumentComposedEventHandler
product_api->billing: AMQP ReadyForBillingEvent
product_api->ofm: FulfilmentRequestCompletedEvent
product_api->ui: Response including data

@enduml
```

The Asynchronous Order Flow is used for ordering products asynchronously i.e. the response to the order creation request does not contain the final data or document, and instead the results must be retrieved at a later date from the appropriate API. This is used either when the application does not require the data immediately after order creation, or for certain products which are retrieved from the vendor asycnhronously in a process which could potentially take weeks.

```plantuml
@startuml

title Asynchronous Order Flow

actor User
participant "Product UI" as ui
participant "Product Microservice" as product_api
participant "External Vendor" as vendor
participant "GSF.OrderFulfilmentManager" as ofm
participant "GSF.Billing" as billing
participant "GlobalX.Intellimanager" as intellimanager

User->ui
ui->product_api: HTTP POST
product_api->RabbitMQ: AMQP FulfilmentRequestReceivedEvent
activate RabbitMQ
product_api->ui: Response with OrderId
RabbitMQ->ofm: AMQP FulfilmentRequestReceivedEvent
RabbitMQ->product_api: AMQP FulfilmentRequestReceivedEvent
deactivate RabbitMQ
product_api->vendor: Retrieve data from vendor API
product_api->intellimanager: AMQP DocumentComposedEventHandler
product_api->billing: AMQP ReadyForBillingEvent
product_api->ofm: FulfilmentRequestCompletedEvent

@enduml
```

### Proxied flows

These Proxied flows achieve the same synchronous and asynchronous functinality described above, but have the order proxied via GlobalX.Web and the GlobalX.Order service. Since the introduction of an API Gateway into the GSF request flow, this mechanism is deprecated but is included for reference as it is widely used by existing products.

```plantuml
@startuml

title Proxied Synchronous Order Flow (Deprecated)

actor User
participant "Product UI" as ui
participant "GlobalX.Web API" as gx_api
participant "GlobalX.Intellimanager" as intellimanager
participant "GlobalX.Order.Endpoint" as order_endpoint
participant "Provider Microservice" as provider
participant "External Vendor" as vendor
participant "GSF.Billing" as billing
participant "GSF.OrderFulfilmentManager" as ofm

User->ui
ui->gx_api: HTTP POST
gx_api->provider: HTTP POST
provider->vendor: Retrieve data from vendor API
provider->order_endpoint: AMQP CreateOrderCommand (contains document)
order_endpoint->intellimanager: AMQP SaveDocumentCommand
provider->billing: AMQP BillingCommand
provider->ofm: AMQP SearchStatusEvent(status=Complete)
provider->gx_api: 200 Order created successfully
gx_api->ui: 200 Order created successfully

@enduml
```

```plantuml
@startuml

title Proxied Asynchronous Order Flow (Deprecated)

actor User
participant "Product UI" as ui
participant "GlobalX.Web API" as gx_api
participant "GlobalX.Order.Service" as order_service
participant "GlobalX.Order.Endpoint" as order_endpoint
participant "GlobalX.Intellimanager" as intellimanager
participant "Provider Microservice" as provider
participant "External Vendor" as vendor
participant "GSF.Billing" as billing
participant "GSF.OrderFulfilmentManager" as ofm

User->ui
ui->gx_api: HTTP POST
gx_api->order_service: HTTP POST
order_service->order_endpoint: AMQP CreateOrderCommand
order_service->gx_api: 200 Order created successfully
gx_api->ui: 200 Order created successfully
order_endpoint->intellimanager: AMQP SaveOrderCommand
order_endpoint->ofm: AMQP SearchCommand
order_endpoint->provider: AMQP SearchCommand
provider->ofm: AMQP SearchStatusEvent(status=Pending)
provider->vendor: Retrieve data from vendor API
provider->intellimanager: AMQP SaveDocumentCommand
provider->billing: AMQP BillingCommand
provider->ofm: AMQP SearchStatusEvent(status=Complete)

@enduml
```
