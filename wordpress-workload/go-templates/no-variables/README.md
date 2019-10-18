# no variables

The simplest case for a stack author is if they want to use some yaml
files, and those yaml files do not use template variables.

## Stack consumer experience

See `user/` for things the stack consumer creates.

### Using the templates

To use the templates, install the stack, then:

```
kubectl apply -f user/resource.yaml
```
