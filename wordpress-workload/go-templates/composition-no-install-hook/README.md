# composition, today

If a stack author wants to compose the resources from multiple other
stacks, they can do that. This example contains configuration for
multiple example stacks.

## Stack author experience

The stacks in this example are the following:

* stack-wordpress, which is a stack composed of all the others
* stack-kubernetes, which wraps the `KubernetesCluster` template
* stack-mysql, which wraps the `MySQLInstance` template
* stack-kubernetesapplication, which wraps the `KubernetesApplication` template

## Stack consumer experience

See `user/` for things the stack consumer creates.

The user first needs to install the `stack-wordpress` stack.

Then, they can create a `WordpressPrerequisites` to signal
`stack-wordpress` to install all of the other stacks:

```
kubectl apply -f user/prerequisites.yaml
```

Then, the user must wait for the other stacks to be installed, so that
the CRDs that the other stacks define can b installed into the cluster.
It's possible that this step can be skipped if the wordpress stack has
sufficiently robust retry logic for creating its resources.

After all the other stacks are installed, the wordpress instance can be
requested:

```
kubectl apply -f user/resource.yaml
```
