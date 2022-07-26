---
title: "User Management"
linkTitle: "User Management"
weight: 2
description: >
  User Management
---

## Introduction

The current architecture for user management does not have a clear distinction between the Search application and GSF Core Services, so this documentation will reference both areas.

Keycloak provides basic account information used for authentication, and is intended tp be application agnostic. This includes username, first name, last name, email address.

GXSDB stores further user profile data relevant for GSF services.

Cross-service reference is provided by Keycloak username to gxsdb userid.

Applications redirect to keycloak for user authentication. The resulting token from Keycloak contains the username, allowing the application to retrieve the correct user from gxsdb or other GSF core user services. 

```plantuml
@startuml gsf_core_auth
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

title GSF Core User Management Container Diagram

Person(person_user, "Our Customer")
Person(person_internal_user, "Internal staff")
System(search_app, "Search application")
System(customer_app, "Customer applications")

Container(Keycloak, "Keycloak Auth Server", "Containerised Java App", "auth.globalx.com.au, providing account details and security APIs")

System_Boundary(c, "GSF Core User Management"){
    Container(GSF_UserAdmin_Service, "GSF.UserAdmin.Service", "C# Microservice", "Provides APIs for internal and customer creation and administration of users administration")
    Container(GSF_Users, "GSF.Users", "C# Microservice", "Provides APIs for internal services to access user data")
    Container(gxs_app_server, "gxs_app_server", "C cgi web server", "Provides web pages for users to manually interact with the user data stored in gxsdb")
    ContainerDb(gxsdb, "gxsdb", "PostgresQL database", "Stores user data")
}

Rel(person_user, gxs_app_server, "uses", "")
Rel(person_internal_user, gxs_app_server, "uses", "")
Rel(person_user, search_app, "uses", "")
Rel(person_user, customer_app, "uses", "")

Rel(search_app, GSF_Users, "uses", "")
Rel(search_app, GSF_UserAdmin_Service, "uses", "")
Rel(customer_app, GSF_Users, "uses", "")
Rel(customer_app, GSF_UserAdmin_Service, "uses", "")

Rel(GSF_UserAdmin_Service, gxsdb, "uses", "")
Rel(GSF_Users, gxsdb, "uses", "")
Rel(gxs_app_server, gxsdb, "uses", "")
Rel(Keycloak, c, "provides authentication for user from", "")

@enduml
```