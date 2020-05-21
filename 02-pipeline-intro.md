# What's in an OpenShift / Tekton Pipeline?

OpenShift Pipelines is based on the upstream [Tekton](https://tekton.dev) project.  The goal of this project is create a cloud-native CI/CD framework that is "serverless" and also portable across any modern Kubernetes distribution.

Since OpenShift is an Enterprise-grade Kuberenetes distribution that is always keeping pace with the latest stable version of Kubernetes, it's only natural that Tekton would not only run nicely in an OpenShift cluster, but it will soon have Red Hat support behind it as well (GA is targetted for OpenShift 4.5).

So, what does a Tekton pipeline consist of?

Tekton consists of a few key elements:
* [Tasks](https://github.com/tektoncd/pipeline/blob/master/docs/tasks.md): Each having at least one step.
* [Pipelines](https://github.com/tektoncd/pipeline/blob/master/docs/pipelines.md): Containing a list of *Tasks* to be executed sequentially or in parallel. 
* [Workspaces](https://github.com/tektoncd/pipeline/blob/master/docs/workspaces.md): Shared storage to allow for state between task runs or pipeline runs.
* [PipelineRuns](https://github.com/tektoncd/pipeline/blob/master/docs/pipelineruns.md): Run a pipeline with a set of inputs and workspaces.
* [TriggerTemplate, EventListener, TriggerBinding](https://github.com/openshift/pipelines-tutorial/tree/release-tech-preview-1#triggers): Used to setup git webook triggers.
* [PipelineResources](https://github.com/tektoncd/pipeline/blob/master/docs/resources.md): Now deprecated.  Still used in this demo, but will be removed soon.

Essentially, each *Task* is simply a container that runs with a set of inputs and instructions.  All *Tasks* in a pipeline run in the same pod, this allows tasks to share share storage allowing for the output of one *Task* to be consumed by a downstream *Task*.

This also means the entire pipeline is codified in standard Kubernetes objects and can be versioned with the rest of your application configuration.

Even better, since a Pipeline defines a set of *Tasks* that are simply containers with defined input and output, there is no "master" server running to maintain configuration or state.  Your pipelines only consume resources when you they run, just like any other "serverless" function.

[Next: Install the CI/CD Tools, Demo App, and Pipelines.](02-install-demo.md)

