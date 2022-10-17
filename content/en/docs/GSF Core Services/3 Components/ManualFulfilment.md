---
title: "Manual Fulfilment"
linkTitle: "Manual Fulfilment"
description: >
  Manual Fulfilment
---

|Quick Links||
|---|---|
|Repo|[ManualFulfilment](https://bitbucket.globalx.com.au/projects/GSF/repos/manualfulfilment/browse)|
|TeamCity|[ManualFulfilment](https://teamcity.globalx.com.au/project/Gsf_ManualFulfilment)|
|K8s Deployment|[manual-fulfilment](https://console.cloud.google.com/kubernetes/deployment/australia-southeast1/dev-k8s/gsf-services/manual-fulfilment/)|
|URL (Prod)|https://ingress.production.globalx.com.au/api/internal/manual-fulfilment|

## Summary
Manual Fulfilment provides functionality to support the manual fulfilment of orders by the Order Fulfilment Team (OFT).\
This includes billing, as well as tools to help with certificate automation.\
It is intended to take over vital functionality from [GSF.Provider.Ess.Qld.Endpoint](https://bitbucket.globalx.com.au/projects/GSF/repos/gsf.provider.ess.qld.endpoint/browse).


## Services

### OrderDataParsingService
This service receives a CDM Payload and parses it into a format that can be used by our UI.Vision Macros and PdfFormFillingService to automatically fill Web Forms and PDFs.\
Information on how UI.Vision is set up ca be found [here](https://dyedurhamau.atlassian.net/wiki/spaces/NPI/pages/10400333843/How+to+set+up+a+certificate+with+Web+Form+automation).

```plantuml
@startuml

title Automatic Fulfilment of A Web Form Order

actor "OFT Member" as User
participant "Order Fulfilment Manager UI" as ofm_ui
participant "Ui.Vision in User's Browser" as ui_vision
participant "Order Fulfilment Manager Endpoint" as ofm_api
participant "Manual Fulfilment" as mf
participant "Authority's Website" as external_site

User->ofm_ui: Click's "Automatically Fulfil" on an Order
ofm_ui->ofm_api: Requests Automation Data 
ofm_api->mf: Sends CDM Payload and Order ID
mf->ui_vision: Sends Parsed CDM Payload Data
ui_vision->external_site: Uses Data to Automatically Fill a Web Form
User->external_site: Confirms and Submits Form


@enduml
```