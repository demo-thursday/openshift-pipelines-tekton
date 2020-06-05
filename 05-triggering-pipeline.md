# Triggering a Pipeline

There is more than one way to trigger a Tekton Pipeline. You can create a `PipelineRun` or trigger your pipeline with a git webhook.  Let's explore both options.

## PipelineRun

When triggering a`Pipeline`, there are a few details you need to provide.  These are provided as part of a `PipelineRun` custom resource.  Let's take a look to see what this looks like:

```
$ cat pipelinerun/run-build.yaml

kind: PipelineRun
apiVersion: tekton.dev/v1alpha1
metadata:
  generateName: petclinic-run-
spec:
  pipelineRef:
    name: maven-build
  resources:
  - name: app-repo
    resourceRef:
      name: app-repo
  workspaces:
  - name: build-workspace
    persistentVolumeClaim:
      claimName: build-workspace-pvc
  - name: local-maven-repo
    persistentVolumeClaim:
      claimName: maven-m2-repo-pvc
```

This resource is actually quite self explanatory, the different components are:

* **generateName:** Instead of having a static name, the name of this `PipelineRun` will be generated to make sure there are no duplicates.
* **pipelineRef:** The name of the `Pipieline` to run.
* **resources:** The list of `PipelineResource`s the `Pipeline` will use.  This is now deprecated.
* **workspaces:** The list of `Workspace`s and their reference.  In this case, both `Workspace` is backed by it's own `PersistentVolumeClaim`.

That's it!  With this we can run our pipeline.

```
$ oc create -f pipelinerun/run-build.yaml
```

Check your "Pipelines" in the `cicd` project in OpenShift and you should see the `maven-build` pipeline is now running.


## Git Webhook



