# PozoleDF - Multiple On Premise Kubernetes Application Delivery Framework

Why Pozole? Well, it was made in Mexico and PO (Private On-premise)-zole sounds like a fun related name, so there you go!

## The Whys

Doing CI/CD in several private on premise environments can be very challenging.
Your restrictions may vary, but this framework tries to address the following ones:

- Cloud Agnostic: Sometimes you don't have permissions, or have limited access to cloud resources.
- Least access possible from the internet: private on premise environments will need that, yes.
- Pull model: private services will pull its configuration from external deployment
  services, instead of updates being pushed to the private environments.
- Docker friendly: ah yes! Please do Docker apps.
- Kubernetes friendly: ah yes! We need high availability, fault tolerance and container orchestration
  in our private environments.
- Continuous Integration/Continuous Delivery Pipelines: Of course! That's an all-time go!

## The What

We built an opinionated framework for addressing the limitations and architecture challenges
described above. We called it PozoleDF. It is formed by the following GitHub repositories:

- [pozoledf](https://github.com/kuritsu/pozoledf). The one you're using now. It will contain all
  architectural and strategical documentation for the project.
- [pozoledf-sample-app](https://github.com/kuritsu/pozoledf-sample-app). Contains a sample NodeJS
  Express application that will expose a health endpoint, so it will work fine as a Kubernetes
  service. It will have everything needed to build a Docker image and a pipeline configuration to
  build it and publish it to a Docker registry.
- [pozoledf-sample-app-deployment](https://github.com/kuritsu/pozoledf-sample-app-deployment). Contains
  the simplest Kubernetes manifests needed to deploy/configure `sample-app` in Kubernetes. It will
  also establish a mechanism for versioning.
- [pozoledf-sample-app-release](https://github.com/kuritsu/pozoledf-sample-app-release). Contains
  configuration files to determine the version of the app to be deployed in 2 different
  environments: one close to development, and another similar to production. It will be based on
  [Chef](https://chef.io) environments and run lists, so it will provide a GitOps opinionated
  structure for application release deployments.
- [pozoledf-chef-install](https://github.com/kuritsu/pozoledf-chef-install). Contains scripts
  for installing and setting up [Chef](https://chef.io) servers and clients, that will glue
  most of the infrastructure components of the solution together.

## The Hows

This is the tech stack we use for this CI/CD solution:

- [GitHub](https://github.com)/Git: Source code repository management.
- [Jenkins](https://www.jenkins.io): Continuous Integration and Delivery pipelines.
- [Chef](https://chef.io): Configuration management/orchestration for on-premise infrastructure.
- [Kubernetes](https://kubernetes.io): Container deployment and orchestration for on-premise applications.
- [Docker Registry](https://docs.docker.com/registry/): Docker artifact repository.
- Linux: We will be using RHEL/CentOS specific tools for all our components.

The following diagram shows the component relationship of the solution:

TODO: Add diagram.

## How to use this framework?

### Infrastructure

You will need the following machines running RHEL 7/CentOS 7 (we suggest you use [Terraform](https://www.terraform.io) if some of these components will hosted in the cloud):
- 1 x Jenkins
  - Run [install-jenkins.sh](https://github.com/kuritsu/pozoledf-chef-install/tree/main/scripts) for setting this up.
- 1 x Chef Infra Server/Chef Automate
  - Run [install-chef-server.sh](https://github.com/kuritsu/pozoledf-chef-install/tree/main/scripts/install-chef-server.sh).
  - [Optional] Run [install-chef-automate.sh](https://github.com/kuritsu/pozoledf-chef-install/tree/main/scripts/install-chef-automate.sh).
- 2 (minimum) x Staging application environment
  - Run [install-k8s-api-server.sh](https://github.com/kuritsu/pozoledf-chef-install/tree/main/scripts/install-k8s-api-server.sh) on the k8s api-server machine.
  - Run [install-k8s-worker.sh](https://github.com/kuritsu/pozoledf-chef-install/tree/main/scripts/install-k8s-worker.sh) on the worker machines, it can also run on the api-server one.
- 2 (minimum) x Production application environment
  - Run [install-k8s-api-server.sh](https://github.com/kuritsu/pozoledf-chef-install/tree/main/scripts/install-k8s-api-server.sh) on the k8s api-server machine.
  - Run [install-k8s-worker.sh](https://github.com/kuritsu/pozoledf-chef-install/tree/main/scripts/install-k8s-worker.sh) on the worker machines, it can also run on the api-server one.

Before running any of the scripts below, **read carefully all the instructions and requirements written
down as comments at beginning of each script**.

You will use your Chef Infra Server/Automate to monitor the status of your nodes,
including the following aspects:
- Jenkins installation and updates
- Application installation and updates, on each environment
- Node active configurations

### Applications

Your applications will follow an opinionated integration/deployment strategy.

- Use [pozoledf-sample-app](https://github.com/kuritsu/pozoledf-sample-app) as a blueprint for setting
  your application integration configuration. Remember to add this project to your Jenkins server.
  The `Jenkinsfile` there will need adjusting, specifically in these aspects:
  - Docker Registry URL
  - Docker Image name and tags
  - Requirements to make `docker build` work.
  - Any additional steps that you'll want to perform, such as:
    - Static code analysis
    - Security analysis
    - Testing and code coverage
- Use [pozoledf-sample-app-deployment](https://github.com/kuritsu/pozoledf-sample-app-deployment)
  as a blueprint for your Kubernetes application configuration. Most of the config files there will
  need adjusting, but we suggest you try to use the [kustomize](https://kustomize.io)-ready files there
  to make it friendlier. You can always use additional K8S resources and plugins.
- Use [pozoledf-sample-app-release](https://github.com/kuritsu/pozoledf-sample-app-release) as
  a blueprint for your GitOps configuration for the release deployments in your configured infrastructure.
  Remember to add this project to your Jenkins server, it will need adjustments to your specific use case.
  Check on the repo's README how to create/configure deployments.

## Contribution

We're open to contributions, as long as they follow our ultimate goals for keeping this framework:
- Simple
- Documented
- Supported
- Extensible
- Opinionated

Please fork any of the repos of the framework and submit a PR.

## About the authors

[Jeffrey Alvarez](https://github.com/kuritsu) is DevOps Tech Lead in Improving Nearshore (known as iTexico in Mexico).
[Abraham Rivera](https://github.com/arivera0000) is a DevOps Engineer in Improving Nearshore.

TODO: Fix Abraham Rivera's GitHub URL.
