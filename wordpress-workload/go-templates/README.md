# go templates

This folder shows what various user scenarios look like using templated
yamls with a go templating engine.

## Template context

The claim's metadata and spec fields will be overlaid (in that order) to
create the template binding.

Configuration can be specified to provide default bindings.

## Developing, building, installing

The examples all assume the same approach for initializing a new stack
project, building it, publishing it, and installing it.

### Initialize

```
kubectl crossplane stack init --template mygroup/mystackname
```

### Add a CRD

The user runs:

```
kubectl crossplane stack crd init WordpressInstance wordpressinstances wordpress.samples.stacks.crossplane.io
```

The output in the user's crd configuration folder is:

```yaml
---
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: wordpressinstances.wordpress.samples.stacks.crossplane.io
spec:
  group: wordpress.samples.stacks.crossplane.io
  names:
    kind: WordpressInstance
    plural: wordpressinstances
  scope: ""
  versions:
  - name: v1alpha1
    served: true
    storage: true
status:
  acceptedNames:
    kind: ""
    plural: ""
  conditions: []
  storedVersions: []
```

The command will also update any `dependsOn` or rbac-oriented
configuration, as appropriate.

An earlier version of the command may partially render a CRD and tell
the user to edit it by hand themselves afterward.

### Build

```
kubectl crossplane stack build
```

### Publish

```
kubectl crossplane stack publish
```

### Install

```
kubectl create namespace stackspace
kubectl crossplane stack install --namespace stackspace mygroup/mystackname mystackname
```

Note that today, `kubectl crossplane stack generate-install` is used to
specify a namespace.

### Uninstall

```
kubectl crossplane stack uninstall --namespace stackspace mystackname
```

## Errors when processing a template

Errors will show up in a couple places:

* In the status of the object which triggered the processing (typically
  an instance of a known CRD)
* In an event which is associated with the object which triggered the
  processing.

## Structure of examples

The examples have three sections:

* A README.md explaining what the example shows
* A `config` folder which shows the structure of the configuration in a
  stack which wants to use the functionality from the example
* A `user` folder, which shows examples of resources the user would
  create

## Covered by the examples

There are a few different things we want to explore when it comes to how
to express templates, and how to configure them. Below is the list of
use-cases the examples cover, and where to find them:

* No variables - see the `no-variables` folder
* Default template bindings - see `default-variables`
* User-provided template bindings - see `user-variables`
* Multiple CRD types - see `multiple-crds`
* Template-based variants - see `variants`
    - Multiple renderings in a single namespace - see `variants`
    - Multiple renderings in different namespaces - see `variants`
* Splitting code across multiple stacks; composition
    - With no install-time template rendering:
      `composition-no-install-hook`
    - With install-time template rendering: `composition-install-hook`

## NOT covered by the examples

The following functionality is intentionally **not** covered by the
examples, as it is out of scope for now. Functionality can be out of
scope if we believe it is not needed yet, and if we believe that the
future implementation will not require fundamental changes to what we're
building now.

* Dynamic name prefixing or suffixing
* Kustomize / overlays
* Live value references
* Auto workload wrapping
* Multiple renderings in a single namespace?
* Auto-creating resources (other than CRDs) when a stack is installed
* Updating output? The high-level idea of re-rendering and letting the
  other stacks take care of the updates seems close for now
* Updating stack? Will happen as part of the other thinking about
  versioning and how to update a stack's version
* Updating stack manager / shared controller?
  - Will need a controller version eventually
* Setting status in the CRD? (Nice to have; seems independent from the
  other stuff we're working on. Seems useful but could be wrong; it
  duplicates information already in the system)

## Open questions

* Should the behaviors.yaml go into app.yaml?
* Should the defaults.yaml go into app.yaml?
* Should dependsOn be in app.yaml?
* What about CRD schema and validation? Are we able to help with generating that?
