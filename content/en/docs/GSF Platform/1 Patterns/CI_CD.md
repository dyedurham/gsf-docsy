---
title: "CI/CD"
linkTitle: "CI/CD"
weight: 3
description: >
  GSF CI/CD Patterns
---

## CI/CD Overview

CI/CD pipelines differ for Kubernetes and Windows applications, but they follow the same general pattern.

```mermaid
graph TD

subgraph "Developer workflow"
  Code("Push code to main/master")
end

Code --> Tests

subgraph "Build Pipeline"
  Tests(Automated Tests)
  Tests --> Build("Build deployable artifact")
  Build --> Push("Push to artifact repository")
end

Push --> Pull

subgraph "Deployment Pipeline"
  Pull("Pull from artifact repository") --> Deploy("Deploy to the appropriate environment")
  Deploy --> Dev("Deploy to Development")
  Deploy --> Staging("Deploy to Staging")
  Deploy --> Prod("Deploy to Production")
end

```

## CI/CD for Kubernetes hosted applications

* Applications are built and packaged by Team City
* Artifacts are Docker Images, pushed to the Docker Container Registry
* Flux is responsible for deployment, using the [GitOps pattern](../3-gitops/)

```mermaid
flowchart TB

subgraph Develop [Developer workflow]
  direction TB
  Code("Push code to main/master")
end

subgraph TeamCity [Team City]
  direction TB
  Tests("Automated Tests") --> Build("Build application into a docker image")
  Build --> Push("Push Docker Image to Docker Container Registry")
end

subgraph Flux ["Flux"]
  direction TB
  Pull("Pushes kubernetes configs to the appropriate cluster")
  Pull --> Dev("Development K8S pulls docker image")
  Pull --> Staging("Staging K8S pulls docker image")
  Pull --> Prod("Production K8S pulls docker image")
end

Code --> TeamCity
TeamCity --> Flux

```


## CI/CD for Windows hosted applications

* Applications are built and packaged by Team City
* Artifacts are Nuget packages, pushed to Octopus
* Octopus is responsible for deployment

```mermaid
flowchart TB

subgraph Develop [Developer workflow]
  direction TB
  Code("Push code to main/master")
end

subgraph TeamCity [Team City]
  direction TB
  Tests("Automated Tests") --> Build("Build application into a Nuget package")
  Build --> Push("Push Nuget package to Octopus Deploy")
end

subgraph Octopus ["Octopus Deploy"]
  direction TB
  Pull("Installs application from Nuget package onto the appropriate Windows VMs")
  Pull --> Dev("Octopus installs onto Development Windows VMs")
  Pull --> Staging("Octopus installs onto Staging Windows VMs")
  Pull --> Prod("Octopus installs onto Production Windows VMs")
end

Code --> TeamCity
TeamCity --> Octopus

```

