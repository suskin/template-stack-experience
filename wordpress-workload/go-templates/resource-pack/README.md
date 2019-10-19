# resource pack

A resource pack is a set of arbitrary resources that a stack author
wants to make available for stack consumers. It could be
provider-specific network configuration, or resource classes, for
example.

**This** resource pack helps a user create the set of baseline GCP
network resources, which is needed if the user wants to run a Wordpress
on GCP. In real-world usage, the user would also need to set up their
GCP provider before creating these network resources, and would need to
do a few other things after creating these network resources. See the
[GCP section of the Stacks
Guide](https://crossplaneio.github.io/docs/v0.3/stacks-guide-gcp.html)
for more details about all the steps involved in the Real World.

## Stack consumer experience

See `user/` for things the stack consumer creates.

The user would install the stack, then:

```
kubectl apply -f user/resource.yaml
```
