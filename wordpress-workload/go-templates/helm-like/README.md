# helm-like

A stack user can create multiple variants of a CRD handled by a template
stack. This is what it would look like if the format was as similar to a
helm chart as possible.

This set of examples is very similar to the `variants` examples, just
the templates are structured more like a helm chart.

The `user` side of the examples is exactly the same as in the `variants`
examples.

## Differences from other examples

To make this example more helm-like, there are a few differences from
other examples:

* The template field paths use helm-style paths (e.g. `.Values.myfield`
  instead of `.myfield`)
* There is a `Chart.yaml`
* The templates are in their own folder, and the `stack.yaml` behaviors
  configuration points to the folder instead of to the individual
  template files.

## Stack consumer experience

See `user/` for things the stack consumer creates.

The stack must be installed first by the user.

### Variant: use the defaults

To use the defaults only:

```
kubectl apply -f user/variant-default.yaml
```

### Variant: two instances with different images

To create two instances with different images:

```
kubectl apply -f user/variant-two-in-same-namespace.yaml
```

### Variant: two instances, different images and namespaces

To create two instances with different images that live in different
namespaces:

```
kubectl apply -f user/variant-two-in-different-namespaces.yaml
```

Note that permissions and garbage collection behaviors on the cluster
may affect how well this example works in a real environment.

## Open questions

### Chart.yaml

For stacks being written from scratch, with the sole purpose of being a
stack, it may feel awkward to write a whole chart (including the
Chart.yaml). If we want to require the Chart.yaml, maybe a template
stack would have its Chart.yaml generated if one does not exist.

### Crossplane workloads

Supporting the actual helm chart format is convenient for people who
want to reuse existing helm charts, but workloads for Crossplane are
specified using types that are Crossplane-specific. More broadly,
anything using Crossplane types would probably not exist as a helm chart
already, so it would require some munging *anyway* to fit it into a
stack. Are we okay with requiring other changes, such as template field
path, or ignoring the Chart.yaml?
