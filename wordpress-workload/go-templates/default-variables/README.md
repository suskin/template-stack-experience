# default variables

A stack author can provide default values for template bindings.

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
