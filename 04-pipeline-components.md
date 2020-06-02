# Pipeline Components

As mentioned earlier, an OpenShift / Tekton Pipeline consiste of `Workspace`s, `Task`s, and `Pipeline`s (as well as PiplineResources, but they are deprecated).  Let's take a look at a few of these componets from the pipeline we will run.

## Workspaces

A [Workspace](https://github.com/tektoncd/pipeline/blob/release-v0.11.x/docs/workspaces.md) is used to share data between tasks.  In our example we have one `Workspace` to store Maven artifacts, one to store build artifacts for the *build* pipeline and one to store build artifacts for the *release* pipeline.  In all three cases, we are using a `PersistentVolumeClaim` for storage.

Let's look at the Maven m2 repository workspace:

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: maven-m2-repo-pvc
spec:
  resources:
    requests:
      storage: 5Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain 
```

This is a standard `PersistentVolumeClaim`.  Nothing fancy here!  We will reference this PVC by name when defining our workspaces in our Pipelines.

## Tasks

A [Task](https://github.com/tektoncd/pipeline/blob/release-v0.11.x/docs/tasks.md) defines a collection of `Steps` that *do something* as part of your pipline.  For example: check out code, run a Maven build, start a deployment, etc...

Let's take a look at the Maven `Task` we have defined:

```
kind: Task
apiVersion: tekton.dev/v1beta1
metadata:
  name: maven
spec:
  workspaces:
    - name: local-maven-repo
      description: Maven local repo dir.
      mountPath: /data/m2
    - name: build-workspace
      description: Build artifacts.
  params:
    - name: GOALS
      description: The Maven goals to run
      type: array
      default: ["package"]
    - name: ARGS
      description: 
      type: string
      default: "-Dbuild=true"
  stepTemplate:
    env:
      - name: "SONAR_USER_HOME"
        value: "$(workspaces.build-workspace.path)/.sonar"
    envFrom:
      - secretRef:
          name: nexus-secret
  steps:
    - name: git-config
      image: maven:3.6.3-jdk-8
      workingDir: $(workspaces.build-workspace.path)
      script: |
          #!/usr/bin/env bash
          echo "Set global git config."
          git config --global user.email "apitt@redhat.com"
          git config --global user.name "Andrew Pitt"
          git config --list
    - name: mvn-build
      image: maven:3.6.3-jdk-8
      workingDir: $(workspaces.build-workspace.path)
      command: ["/usr/bin/mvn"]
      args:
        - "$(params.ARGS)"
        - -s
        - /var/mvn/settings.xml
        - "$(params.GOALS)"
        - -e
      volumeMounts:
        - name: maven-settings
          mountPath: /var/mvn
  volumes:
    - name: maven-settings
      configMap:
        name: maven-settings-cm

```

Now this has some content!

### Kind, ApiVersion, Name

First, you have the standard *kind, apiVersion, and name*.  Standard stuff so far.

### Workspaces

Next as part of the `spec` we declare the *workspaces* we will be using and (optiona) mount path for each.  For this `Task` we are using two *workspaces*.  One for the Maven `m2` cache directory, and one for the project source code and build artifacts.

### Params

Next, we define any *parameters* that can be passed into this task and default values to go with them.  There are two params for this *task*, `GOALS` and `ARGS`.  You can reference these params in the *steps* of the *Task*.

### StepTemplate

The *stepTemplate* stanza provides configuration for all *steps* in the *Task*.  Since all *steps* are part of the same running container, the *stepTemplate* is also where you would set any environment variables that you want injected into the container for the task.  For this task, we are injecting the `SONAR_USER_HOME` environment variable, as well as the Nexus useranem and password based on the `nexus-secret` secret.

###  Steps

The *steps* of a *Task* represent the sequence of events needed to complete the task.  For our Maven task, this broken up into two steps:  `git-config` and `mvn-build`.  The git config step is fairly trivial and can probably be replaced with environment variables... but for now, it's a step in this *Task*!

The `mvn-build` *step* executes a set of Maven goals based on the **GOALS** that were passed in as a parameter.  As you can see, this *step* also sets the `workingDir` to the path of the build *workspace*.  It also makes reference to a `settings.xml` file that is mounted into the container at the bottom of the *step* definition.

### Volumes

Finally, 


[Next: Trigger a Pipeline Run](05-triggering-pipeline.md)