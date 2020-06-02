# OpenShift Pipelines - Porting a Jenkins Pipeline to a Tekton Pipeline

**Original Demo Date: 2020-05-21**

**Updated for `v1beta1` spec: 2020-06-01**

## What is OpenShift Pipelines?

[OpenShift Pipelines](https://www.openshift.com/learn/topics/pipelines) is a framework for creating cloud-native CI/CD pipelines in an OpenShift environemnt.  OpenShift Pipelines is based on the upstream [Tekton](https://tekton.dev/) project that is part of the [Continuous Delivery Foundation](https://cd.foundation/). 

As of today's Demo, OpenShift Pipelines is now in *Tech Preview*.

For lots of great info on OpenShift Pipelines and a number of additional resources, please take a look at the recent [post on the OpenShift blog about OpenShift Pipelines Tech Preview](https://www.openshift.com/blog/openshift-pipelines-tech-preview-blog).
 

For this demo, we will take the Jenkins pipeline that we used in a [previous demo](https://github.com/demo-thursday/cicd-openshift-jenkins) and re-create it using OpenShift Pipelines!  Then, for fun we will also add a "Release" pipeline

There's lots to get through, so let's get started!

## Prerequisites

For the full demo, you will need an OpenShift 4.4 (or newer) cluster.  This will also run on OpenShift 4.3, but the Developer dashboards will not be quite as detailed.

You can also run this lab using [CodeReady Containers](https://developers.redhat.com/products/codeready-containers/overview) on your local machine, however, it will take a lot of resources.  If you do want to use CodeReady Containers:
* Edit the `kustomization.yaml` file found in the `infra` directory and delete the line `- github.com/redhat-canada-gitops/10-devtools/sonarqube8/overlays/plugins?ref=master
`.  SonarQube takes a lot of resources, and it may simply fail to start in CodeReady Containers unless you have a heavy duty dev machine.
* Edit `tekton/pipelines/build-pipeline.yaml` and remove the `sonar:sonar` **GOAL**.

On to the setup!

[Next: Setting Up OpenShift Pipelines](01-setup.md)
