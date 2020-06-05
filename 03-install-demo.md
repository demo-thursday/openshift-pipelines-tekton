# Install CI/CD Tools, Demo App, and Pipelines!

Our cluster is ready for Tekton pipelines, so let's setup the rest of the infrastructure now.

## Install Nexus and SonarQube

First, let's install Nexus and SonarQube.  This is done with Kustomize:

```
$ oc apply -k infra
```

This will setup and configure Nexus and SonarQube.  It will also create a `ConfigMap` containing a `settings.xml` file that will be mounted into the *Maven* task container, and a `Secret` with the Nexus credentials.

Depending on how much horsepower your cluster has, it may take 2-3 minutes for Nexus and SonarQube to start and configure.  You can should be able to see them spinning up in the `cicd` project.

## Install the Demo App

We also need an application to deploy!  Let's create two environemnts (DEV and UAT) for the trusy Petclinic application.

```
$ oc apply -k app/overlays/all
```

A few things to notice:
1. This will create two projects: *petclinic-dev* and *petclinic-uat*
2. It will create `RoleBinding`s giving the `pipeline` `ServiceAccount` *admin* access to the DEV and UAT projects.
3. You might not have noticed, but part of the "infra" setup also setup the petclinic service accounts from "DEV" and "UAT" to be "image-pullers" on the `cicd` project.

Ok!  Nexus, SonarQube, and the DEV/UAT app environments are in place, time to move on to the Pipelines.

## Create Some Pipelines!

OpenShift Pipelines are made up of Tekton *Tasks, Workspaces, Pipelines, and Triggers*, among other things.  

If you want to simply run the `maven-build` pipeline, then you are good to go.  If you want to run the `maven-release` pipeline, then you will need to:
1. Fork https://github.com/pittar/spring-petclinic to your own GitHub account.
2. Update `tekton/pipelines/build-pipeline.yaml` to point to your Github repo.
3. Update `tekton/resources/github-secret.yaml` with your Github username and password.

Ok, now let's create your the pipelines:
```
$ oc apply -k tekton/
```

This will create everything you need to run the pipelines.  Before we start them, let's take a deeper look at the different components of the pipelines.


[Next: Trigger a Pipeline Run](04-pipeline-components.md)