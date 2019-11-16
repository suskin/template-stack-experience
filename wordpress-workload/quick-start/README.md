# Quick start

This shows what the quick-start experience would look like for a user
trying to deploy a stack-based application onto a cloud-backed
kubernetes cluster using Crossplane and template stacks.

## How to use this example

This example shows several different stacks coming together to enable a
user to easily deploy an application using Crossplane and Stacks,
similar to what was shown in episode 5 of TBS.

This README lists the user steps for the experience, in the [User
Prerequisites](#user-prerequisites) and [User
Steps](#user-steps) sections.

This repository contains realistic and complete examples for the stacks
and other resources needed to walk through the experience.

In the examples, the following is shown:

* The format of `stack.yaml` (see `example.stack.yaml` for even more
  explanation).
* What it would look and feel like to process and create resources at
  "configure" time, when a stack is first installed.
* How multiple different resource processing engines may be specified
  and configured. Included are `kustomize`, `go-kustomize`, and `helm2`.
* What a resource pack would look like.
* What it would look and feel like to load a stack directly from a git
  repository.
* What it would look and feel like to override template values, for
  multiple engines.

Here's a guide to the different stacks in this example:

* `infra-stacks/` doesn't have any stack definitions; just resource
  yamls for installing infrastructure stacks.
* `resource-packs/dev/gcp/` has a stack definition for a resource pack
  for setting up infrastructure quickly on GCP.
* `app-stack/go-kustomize/` has a stack definition for an app stack
  which creates a WordPress workload when the stack is installed. It
  uses go templates and kustomize to process the resource files.
* `app-stack/helm2/` has a stack definition for an app stack
  which creates a WordPress workload when the stack is installed. It
  uses the helm 2 templating engine to process the resource files.

## User Prerequisites

Before the experience described in this example is relevant, the user
must have already completed the following steps.

1. Set up a k8s cluster
2. Install Crossplane onto that cluster
3. Create an account on the desired cloud provider

## User steps

### Install infrastructure stacks

First, install the desired infrastructure stacks.

```
kubectl apply -f infra-stacks/gcp/install.yaml
```

### Configure infrastructure stacks

Next, configure the infrastructure stacks' providers. This is out of the
scope of this example, but one could imagine a resource pack which makes
it easier to configure a provider.

### Install resource packs

Next, install the desired resource packs to set up your cloud
infrastructure quickly.

Install the stack:

```
cat > install-resource-pack.yaml <<EOF
apiVersion: stacks.crossplane.io/v1alpha1
kind: StackInstall
metadata:
  name: "gcp-resource-pack-stack"
  namespace: dev
spec:
  package: "github.com/suskin/template-stack-experience/wordpress-workload/quick-start/resource-packs/dev/gcp"
EOF

kubectl apply -f install-resource-pack.yaml
```

Apply the pack:

```
cat > apply-resource-pack.yaml <<EOF
apiVersion: gcp.wordpress.samples.stacks.crossplane.io/v1alpha1
kind: ResourcePack
metadata:
  name: "gcp-resource-pack"
  namespace: dev
EOF

kubectl apply -f apply-resource-pack.yaml
```

### Install app stack

Next, install the app stack.

#### Option 1: using the go-kustomize engine

Install the stack:

```
cat > install-app-stack.yaml <<EOF
apiVersion: stacks.crossplane.io/v1alpha1
kind: StackInstall
metadata:
  name: "my-wordpress-stack-go-kustomize"
  namespace: dev
spec:
  # This can be a git url or a docker image repository
  package: "github.com/suskin/template-stack-experience/wordpress-workload/quick-start/app-stack/go-kustomize"
EOF

kubectl apply -f install-app-stack.yaml
```

Create an object to tell the stack to deploy the application:

```
cat > deploy-app.yaml <<EOF
apiVersion: wordpress.samples.stacks.crossplane.io/v1alpha1
kind: WordpressInstance
metadata:
  name: "my-wordpress-app-from-go-kustomize"
  namespace: dev
spec:
  engineVersion: "8.0"
EOF

kubectl apply -f deploy-app.yaml
```

#### Option 2: using the helm2 engine

Install the stack:

```
cat > install-app.yaml <<EOF
apiVersion: stacks.crossplane.io/v1alpha1
kind: StackInstall
metadata:
  name: "my-wordpress-stack-helm2"
  namespace: dev
spec:
  package: "github.com/suskin/template-stack-experience/wordpress-workload/quick-start/app-stack/helm2"
EOF

kubectl apply -f install-app.yaml
```

Create an object to tell the stack to deploy the application:

```
cat > deploy-app.yaml <<EOF
apiVersion: wordpress.samples.stacks.crossplane.io/v1alpha1
kind: WordpressInstance
metadata:
  name: "my-wordpress-app-from-helm2"
  namespace: dev
spec:
  engineVersion: "8.0"
EOF

kubectl apply -f deploy-app.yaml
```

### Wait and use

That's it! Everything should be provisioning. You can check the progress
with `kubectl crossplane trace --namespace dev kubernetesapplication
wordpress-demo`.
