---
title: "GitOps"
linkTitle: "GitOps"
weight: 4
description: >
  Introduction to GSF GitOps
---

## What is GitOps?

GitOps is a way to define desired state of infrastructure and application deploys in code.

It allows us to apply best practices from coding to infrastructure and deployment management, such as Pull Requests, traceability, testability and reproduceability.

Flux is the tool we use to translate desired state defined as code in a Git repository into running application state in our live kubernetes clusters.

For further introduction to GitOps and Flux concepts see https://www.weave.works/technologies/gitops/.

## Workflows

### Application Development workflow

This workflow describes how a developer would make code changes to an application, such as a GSF2 microservice or microsite

* Developer checks in application code to application repository, via the standard development workflow (branch, PR, merge checks, merge to master)
* Teamcity executes CI/CD steps (build, test, vulnerability scans etc.)
* Teamcity pushes docker image to GCR

```plantuml
@startuml
!includeurl https://raw.githubusercontent.com/matthewjosephtaylor/plantuml-style/master/style.pu

"Developer"

database "Git Repository"{
    [Application Code]
}

[Team City]

database "GCP Container Registry"{
    [GCR]
}

[Developer] -> [Application Code] : Read/Write
[Team City] -> [Application Code] : Read
[Team City] --> [GCR] : Write\n(Push Docker Image)

@enduml
```


### Application Deployment & Configuration workflow

This workflow describes how a developer would make changes to the development/staging/prod kubernetes environment, such as deploying a different application version or configuring kubernetes resources (scaling a deployment, update configuration values, updating ingress rules etc.)

* Developer makes changes to the [flux-gsf repository](https://bitbucket.globalx.com.au/projects/CLD/repos/flux-gsf/browse) via the standard development workflow:
  * Changes made in a branch
  * Create PR
  * PR is verified automatically (Team City) and manually (approvals from list of designated users)
  * Branch is merged to master
* Flux application polls the [flux-gsf repository](https://bitbucket.globalx.com.au/projects/CLD/repos/flux-gsf/browse) for changes and applies to the cluster
* When image update automation is enabled in development, staging:
  * flux scans GCR for image updates
  * flux writes the new image version to the flux-gsf repo
  * updated image is picked up by the next poll attempt

```plantuml
@startuml
!includeurl https://raw.githubusercontent.com/matthewjosephtaylor/plantuml-style/master/style.pu

"Developer"

database "Git repository\nflux-gsf"{
    [Branch]
    [Master]
}

frame "QA Pipeline"{
    [Team City Validations]
    [Merge Checks]
}

frame "Kubernetes Cluster" {
    [Flux Application] as Flux
    [Deployed Applications]
}

frame "Artifact Repositories" as Artifacts {
    [Container Registries\ne.g. GCR, Dockerhub] as Containers
    [Helm Chart Repositories\ne.g. Github, Bitbucket] as Helms
}

[Developer] -right-> [Branch] : Write
[Branch] .down.> [Master] : Merge
[Branch] -right-> [Team City Validations]
[Team City Validations] -down-> [Merge Checks]
[Merge Checks] -left-> [Master]
[Flux] -up-> [Master] : Read
[Flux] -up-> [Master] : Write (Image Updates)
[Flux] -> [Deployed Applications] : Write\n(Sync Changes)

[Flux] --> Artifacts : Read

@enduml
```

**Access control notes**

* Bitbucket access is limited to office IP addresses
* Developer read/write access to Bitbucket is secured by Bitbucket account username/password or ssh key
* Flux application access to Bitbucket is secured by ssh key
* Developer access to Kubernetes cluster is Read only
* Developers can have Read-only access temporarily elevated to Write through the Permissions Request process, which is managed by the SecOps team

## Sources

* Flux V2: https://bitbucket.globalx.com.au/projects/CLD/repos/flux-gsf/browse
* Flux V1 installation: https://bitbucket.globalx.com.au/projects/CLD/repos/flux/browse
* Flux V1 application management: https://bitbucket.globalx.com.au/projects/CLD/repos/gsf-services-deployment/browse

## References

* https://www.weave.works/technologies/gitops/
* https://fluxcd.io/
* https://github.com/fluxcd/flux2
