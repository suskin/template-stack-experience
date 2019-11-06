# variants

A stack user can create multiple variants of a CRD handled by a template
stack.

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
