# PozoleDF - Multiple On Premise Kubernetes Application Delivery Framework

Why Pozole? Well, it was made in Mexico and PO (Private On-premise)-zole sounds like a fun related name,
and we certainly love pozole, so there you go!

## The Whys

Doing CI/CD in several private on premise environments can be very challenging.
Your restrictions may vary, but this framework tries to address the following ones:

- Cloud Agnostic: Sometimes you don't have permissions, or have limited access to cloud resources.
- Least access possible from the internet: private on premise environments will need that, yes.
- Pull model config: private services will pull its configuration from external deployment
  services, instead of updates being pushed to the private environments.
- Push model telemetry: Deployed services and infrastructure will push their logs and metrics to the
  monitoring dashboards and services.
- Docker friendly: ah yes! Please do Docker apps.
- Kubernetes friendly: ah yes! We need high availability, fault tolerance and container orchestration
  in our private environments.
- Continuous Integration/Continuous Delivery Pipelines: Of course! That's an all-time go!

## The What

We built an opinionated framework for addressing the limitations and architecture challenges
described above. We called it PozoleDF. It is formed by the following GitHub repositories:

- [pozoledf](https://github.com/kuritsu/pozoledf). The one you're using now. It will contain all
  architectural and strategical documentation for the project.
- [pozoledf-chef-repo](https://github.com/kuritsu/pozoledf-chef-reo). This will provide the install
  scripts together with the Chef cookbooks, policies and environment configuration. Check the README
  for a guide on installing every node by role.
- [pozoledf-sample-app](https://github.com/kuritsu/pozoledf-sample-app). Contains a sample NodeJS
  Express application that will expose a health endpoint, so it will work fine as a Kubernetes
  service. It will have everything needed to create a Docker image and a pipeline configuration to
  build and publish it to a Docker registry.
- [pozoledf-sample-app-deployment](https://github.com/kuritsu/pozoledf-sample-app-deployment). Contains
  3 deployment configuration elements for the Sample App:
  - Chef Habitat package to deploy the manifests in the Kubernetes controller.
  - YAML file manifests needed to deploy/configure the app in Kubernetes.
  - Environment configuration, to determine which versions of the app/package will be deployed
    in which environment. For more details, check the README file of the repo.

## The Hows

This is the tech stack we use for this CI/CD solution:

- [GitHub](https://github.com)/Git: Source code repository management, versioning and release definition.
- [Jenkins](https://www.jenkins.io): Continuous Integration and Delivery pipelines.
- [Chef Server Infra](https://www.chef.io/products/chef-infra): Configuration management/orchestration for on-premise infrastructure.
- [Chef Habitat](https://www.chef.io/products/chef-habitat): Application building, packaging, versioning, and deployment.
- [Chef Automate](https://www.chef.io/products/chef-automate): Visibility on the Chef Server Infra and Chef Habitat applications.
- [Kubernetes](https://kubernetes.io): Container deployment and orchestration for on-premise applications.
- [Docker Registry](https://docs.docker.com/registry/): Docker artifact repository.
- [Grafana](https://grafana.com/), [ElasticSearch](https://www.elastic.co/elasticsearch/), [InfluxDB](https://www.influxdata.com/): Infrastructure, service and application monitoring/telemetry.
- Linux: We will be using RHEL/CentOS specific tools for all our components.

The following diagram shows the component relationship of the solution:

![Network Architecture](network-architecture.png?raw=true "Network Architecture")

## How to use this framework?

### Infrastructure

Check the [README](https://github.com/kuritsu/pozoledf-chef-repo/blob/main/README.md) on the
[pozoledf-chef-repo](https://github.com/kuritsu/pozoledf-chef-repo) repo.

You will use your Chef Infra Server/Automate to monitor the status of your nodes,
including the following aspects:
- Jenkins installation and updates
- Monitor server installation, which will contain Elasticsearch, InfluxDb and Grafana.
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
  Keep in mind that when the main branch changes in this repo, the pipeline will trigger the
  [release](https://github.com/kuritsu/pozoledf-sample-app-deployment) pipeline,
  by creating a new release branch in that repository.
- Use [pozoledf-sample-app-deployment](https://github.com/kuritsu/pozoledf-sample-app-deployment)
  as a blueprint for your release and deployment configuration. Most of the config files there will
  need adjusting, but we suggest you use the [kustomize](https://kustomize.io)-ready files there
  to make it friendlier. You can always use additional K8S resources and plugins, as well
  as changing the default Chef Habitat package config to enrich the deployment steps. Check the README of the repo
  for more details.
- Use [pozoledf-chef-repo](https://github.com/kuritsu/pozoledf-chef-repo) to define your
  Chef cookbook for installing the application using Habitat and its supervisor running on the Kubernetes control plane. For an example check the `cookbooks/pozoledf-sample-app` directory of that repo. Note that that cookbook is already included on the `k8s-controller.json` inside the `roles` dir.

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

[Abraham Rivera](https://github.com/Abraham83) is a DevOps Engineer in Improving Nearshore.
