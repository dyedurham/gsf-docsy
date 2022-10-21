---
title: "Authentication and Authorization"
linkTitle: "Authentication and Authorization"
description: >
  Authentication and Authorization features
---

# User authorization

APIs require a valid JWT to be present in the Authorization header. This JWT can be obtained from Keycloak. The Matters UI Flutter SPA uses OpenID Connect with Auth Code + PKCE flow to obtain the token.

# Fine Grained Authorization

In order to determine user access to specific resources, the system uses policy based authorization. A product called Open Policy Agent (OPA) is used to store and evaluate policies, allowing complex and frequently changing business rules to be separated from application code for maintainability and flexibility.

An example of a Policy is: can User X access Matter Y? A user can access a matter if:
* they are part of the firm that owns the matter
* they are added as an external contact onto the matter

When a request is made to GSF.Matters for a matter, the service will ask OPA. Given the User Id and the Matter Id, OPA will retrieve the data required to evaluate this policy and return the result.

{{% alert title="TODO" color="warning" %}}
See diagram 'Policy Based Authorization' on Miro 'Unity Core Designs'
https://miro.com/app/board/o9J_lq7M_yQ=/?moveToWidget=3458764536104370529&cot=14
{{% /alert %}}

{{< figure src="../policy_components.png" >}}

{{< figure src="../policy_sequence.png" >}}