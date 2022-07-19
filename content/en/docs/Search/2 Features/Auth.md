---
title: "Authentication and Authorization"
linkTitle: "Authentication and Authorization"
description: >
  Authentication and Authorization features
---

## GXID Cookies

All UIs and many APIs in the Search application are protected using gxid cookie auth.

GXID (GlobalX ID) is a unique token that identifies a user session. When using a gxid auth flow, a user is assigned a gxid after authenticating. The gxid is the key to the session stored in the GXSDB sessions table. A common use of the GXID is for the client to include it in a cookie in subsequent requests. GlobalX services can then validate the user session and retrieve the associated user and user details.

In most cases, microservices don't need to know about GXID but instead can rely on a JWT being present in the request. Gxid authorization is done at the API gateway level (Kong), and Kong converts the GXID into a JWT. This means our microservices can be OAUTH2 compatible, where the user explicitly provides the JWT rather than the GXID.

### GXID Cookie using Password Flow

In this flow, the login microsite collects the username and password from the user and verifies with keycloak using the Password Flow.

```plantuml
@startuml
Title GXID Cookie Auth using OIDC Password Flow

group Unauthorized

Browser->Kong: online.globalx.com.au/Web
note right of Kong
Unauthorised Handler plugin- no gxid cookie
found so redirects to login page
end note

Kong->Browser: 302\nonline.globalx.com.au/CookieAuth.dll (Login Page)

end Unauthorized

group Authorization

Browser->gsf.login.UI: online.globalx.com.au/CookieAuth.dll (Login Page)
gsf.login.UI->Browser: Login Page

Browser->GSF.Common.Security: POST /api/security/authenticate\nusername,password

activate GSF.Common.Security
GSF.Common.Security->Keycloak: /token\nPassword Grant (username,password)
Keycloak->GSF.Common.Security: JWT
GSF.Common.Security->gxsdb: Check user exists\nCreate gxid session
GSF.Common.Security->Browser: [GXID cookie]
deactivate GSF.Common.Security

end Authorization

group Authorized

Browser->Kong: online.globalx.com.au/Web\n[GXID Cookie]

activate Kong
Kong->GSF.Common.Security: Unauthorised Handler plugin retrieve gxid session details
GSF.Common.Security->gxsdb: Retrieve gxid session details
note right of Kong
Unauthorised Handler plugin creates JWT with details from gxid session
Proxy request + JWT to GlobalX.Web
end note
Kong->GlobalX.Web: /Web\n[JWT]
deactivate Kong

end Authorized
@enduml
```

### GXID Cookie using SSO

In this flow, the application uses the OIDC Auth Code flow to redirect the user to keycloak for authentication. Keycloak then further redirects the user to the target external identity provider using a standard SSO protocol such as OIDC or SAML. In this example, the unauthenticated user is redirected to the login page, which initiates the OIDC Auth Code flow.

```plantuml
@startuml

title GXID Cookie Auth using OIDC Auth Code Flow (Enterprise Login)
participant "Browser/User Agent" as Browser

group Unauthorized

Browser->Kong: online.globalx.com.au/Web
note right of Kong
Unauthorised Handler plugin- no gxid cookie
found so redirects to login page
end note
Kong->Browser: 302\nonline.globalx.com.au/CookieAuth.dll (Login Page)

end Unauthorized

group Authorization

note right of Browser
User enters their email into the 'Enterprise Login' section
end note

Browser->Security: /api/security/oidc/login?login_hint=user@domain.com
Security-->Browser: 302 keycloak /auth (OIDC auth code endpoint)


group Auth code flow steps

Browser->Keycloak: GET keycloak /auth (OIDC auth code endpoint)
Keycloak->Browser: return login page\n(or 302 to external provider)
Browser->Keycloak: POST Authenticate & consent\n(post to external endpoint instead of Keycloak for external provider)
Keycloak->Browser: 302 redirect /api/security/oidc/handler?code=... (OAuth2 redirect callback handler)
Browser->Security: /api/security/oidc/handler?code=...
Security->Keycloak: /token
note over Security,Keycloak: Swap Code for Tokens
Keycloak-->Security: Access & ID Token (JWT)
end Auth code flow steps

note right of Security
Check user exists in GXSDB
Create gxid session in gxsdb
Create gxid cookie
end note
Security->Browser: 200 [gxid cookie]

end Authorization

group Authorized

Browser->Kong: Request for a resource with [gxid cookie] e.g. /apps/edr microsite
note right of Kong: check for valid gxid against gxsdb
Kong->Kong: Proxy request to upstream resource
Kong-->Browser: Resource

end Authorized

@enduml

```



## Identity Server JWT

## Kong API Keys