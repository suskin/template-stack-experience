# multiple crds

A stack author can specify how to respond to more than one CRD. They
don't necessarily need to be CRDs which are part of the Stack, but we
expect that they often will be.

## Stack consumer experience

See `user/` for things the stack consumer creates.

To use the wordpress instance CRD:

```
kubectl apply -f user/wordpress.yaml
```

To use the mysql instance wrapper CRD:

```
kubectl apply -f user/mysql.yaml
```
