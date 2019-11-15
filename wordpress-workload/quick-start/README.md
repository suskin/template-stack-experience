# Quick start

This shows what the quick-start experience would look like for a user
trying to deploy a stack-based application onto a cloud-backed
kubernetes cluster using Crossplane and template stacks.

## How to use this example

This example shows several different stacks coming together to enable a
user to easily deploy an application using Crossplane and Stacks,
similar to what was shown in episode 5 of TBS.

This README lists the user steps for the experience.

This repository contains realistic and complete examples for the stacks
and other resources needed to walk through the experience.

In the examples, the following is shown:

* The format of `stack.yaml`.
* What it would look and feel like to process and create resources at
  "configure" time, when a stack is first installed.
* How multiple different resource processing engines may be specified
  and configured. Included are `kustomize`, `go-kustomize`, and `helm2`.
* What a resource pack would look like.
* What it would look and feel like to load a stack directly from a git
  repository.
* What it would look and feel like to override template values, for
  multiple engines.

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

```
cat > install-resource-pack.yaml <<EOF
apiVersion: stacks.crossplane.io/v1alpha1
kind: StackInstall
metadata:
  name: "gcp-resource-pack"
  namespace: dev
spec:
  package: "github.com/suskin/template-stacks-experience/wordpress-workload/quick-start/resource-packs/dev/gcp"
EOF

kubectl apply -f install-resource-pack.yaml
```

### Install app stack

Next, install the app stack.

#### Option 1: using the go-kustomize engine

Using the go-kustomize engine:

```
cat > install-app.yaml <<EOF
apiVersion: stacks.crossplane.io/v1alpha1
kind: StackInstall
metadata:
  name: "my-wordpress"
  namespace: dev
spec:
  # This can be a git url or a docker image repository
  package: "github.com/suskin/template-stacks-experience/wordpress-workload/quick-start/app-stack/go-kustomize"

  # A stack can also be installed directly from an image or url
  # which does not have its own stack configuration. In that case,
  # the stack configuration can be specified in the stack install itself.
  # stackConfiguration:
  #   title: "Wordpress Stack"
  #   configure:
  #     - directory: configure/
  #       engine:
  #         type: go-kustomize
  #         configuration:
  #           data:
  #             imageid: "wordpress:5-fpm-alpine"


  # In the case that the stack has a "configure" phase hook, the stack install
  # can also specify configuration to be passed directly to the engine processing
  # the hook. The configuration is namespaced so that it doesn't collide with any
  # other configuration. In a non-install CRD, it would not need to be namespaced
  # so aggressively, because the CRD may only be used for triggering and configuring
  # a resource processing engine.
  configure:
    data:
      engineVersion: "5.7"
EOF

kubectl apply -f install-app.yaml
```

#### Option 2: using the helm2 engine

Using the helm2 engine:

```
cat > install-app.yaml <<EOF
apiVersion: stacks.crossplane.io/v1alpha1
kind: StackInstall
metadata:
  name: "my-wordpress"
  namespace: dev
spec:
  package: "github.com/suskin/template-stacks-experience/wordpress-workload/quick-start/app-stack/helm2"
  configure:
    data:
      engineVersion: "5.7"
EOF

kubectl apply -f install-app.yaml
```

### Wait and use

That's it! Everything should be provisioning. You can check the progress
with `kubectl crossplane trace --namespace dev kubernetesapplication
wordpress-demo`.
