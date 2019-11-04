# default variables

A stack author can provide default values for template bindings. The
defaults are specified by setting default field values in the CRDs.

The reason to use default field values in CRDs instead of a separate set
of default bindings is that the CRD defaulting mechanism already exists,
so we wouldn't need to invent a new one.

## Stack consumer experience

See `user/` for things the stack consumer creates.

### Using only defaults

To use the defaults only, install the stack, then:

```
kubectl apply -f user/resource.yaml
```

### Combining defaults with overrides

To override the defaults, install the stack, then:

```
kubectl apply -f user/resource-with-overrides.yaml
```
