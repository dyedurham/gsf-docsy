---
title: "Manual Fulfilment"
linkTitle: "Manual Fulfilment"
date: 2022-10-26
description: >
  Manual Fulfilment Overview
---


|Components||
|---|---|
|Microservice|[Manual Fulfilment]({{< relref "./ManualFulfilment.md" >}})|
|Microsite|None|


## Summary
Manual Fulfilment provides functionality to support the manual fulfilment of orders by the Order Fulfilment Team (OFT).\
This includes billing, as well as tools to help with certificate automation.\
It is intended to take over vital functionality from [GSF.Provider.Ess.Qld.Endpoint](https://bitbucket.globalx.com.au/projects/GSF/repos/gsf.provider.ess.qld.endpoint/browse).

## Structure
Manual Fulfilment is an API only services, used only by our own internal services.
It is a RESTful API.

### Technologies
- .NET Core
- Serilog
- RabbitMQ


### Connected Services
|Service|Connection|
|---|---|
|[Order Fulfilment Manager]({{< relref "../Order Fulfilment Manager/_index.md" >}})|OFM uses ManualFulfilment to parse data for Automation|
|CDM|It uses CDM payloads as a data format for multiple functions|
|Document Assembly|To get PDF templates for PDF automation in OFM|
|Certificate Store|Get info about certificates so it can bill customers for them|


