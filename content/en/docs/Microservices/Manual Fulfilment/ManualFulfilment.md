---
title: "Manual Fulfilment API"
linkTitle: "Microservice (API)"
date: 2022-10-26
description: >
  Manual Fulfilment Microservice (API)
---

|Quick Links||
|---|---|
|Repo|[ManualFulfilment](https://bitbucket.globalx.com.au/projects/GSF/repos/manualfulfilment/browse)|
|TeamCity|[ManualFulfilment](https://teamcity.globalx.com.au/project/Gsf_ManualFulfilment)|
|K8s Deployment (Dev)|[manual-fulfilment](https://console.cloud.google.com/kubernetes/deployment/australia-southeast1/dev-k8s/gsf-services/manual-fulfilment/overview?project=glx-development-au)|
|URL (Prod)|https://ingress.production.globalx.com.au/api/internal/manual-fulfilment|



## Services
Below are the services inside Manual Fulfilment.\
This list is currently incomplete.

### OrderDataParsingService
This service receives a CDM Payload and parses it into a format that can be used by our UI.Vision Macros and PdfFormFillingService to automatically fill Web Forms and PDFs.\
Information on how UI.Vision is set up can be found [here](https://dyedurhamau.atlassian.net/wiki/spaces/NPI/pages/10400333843/How+to+set+up+a+certificate+with+Web+Form+automation).

```plantuml
@startuml

title Automatic Fulfilment of A Web Form Order

actor "OFT Member" as User
participant "Order Fulfilment Manager UI" as ofm_ui
participant "Order Fulfilment Manager Endpoint" as ofm_api
participant "Manual Fulfilment" as mf
participant "Ui.Vision in User's Browser" as ui_vision
participant "Authority's Website" as external_site

User->ofm_ui: Clicks "Automatically Fulfil" on an Order
ofm_ui->ofm_api: Requests Automation Data 
ofm_api->mf: Sends CDM Payload and Order ID
mf->ofm_api: Sends Parsed CDM Payload Data
ofm_api->ofm_ui: Sends Parsed CDM Payload Data
ofm_ui->ui_vision: Request to Start Macro with Data
ui_vision->external_site: Automatically Fills Web Form
User->external_site: Confirms and Submits Form


@enduml
```

### Pdf