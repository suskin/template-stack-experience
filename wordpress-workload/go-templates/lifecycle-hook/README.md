# lifecycle-hook

Design Status: Speculative

## What "Speculative" means in this context

This is the most speculative of the experiences. We expect it to be
refined more in the future as we learn more, so the idea is to spend
**some** time thinking about what the experience **could** look like,
but to aggressively timebox the time spent on this for now, so that we
can focus on making progress on the configuration side. Since we expect
to learn more about what we think the best experience would be for
specifying hooks as we work on the configuration side, getting into the
work on the configuration side is a higher priority than settling on a
design for the hook experience.

## Overview

The goal of a lifecycle hook is to allow application authors to run some
custom logic when interesting events happen. Existing projects in this
space include:

* Writing a custom controller on top of the kubernetes libraries
* Admission webhooks
* Operator Framework
* Metacontroller
* kubebuilder
* KUDO

The Kubernetes term for such logic is "controller" logic.

## Terminology

* A **target resource** is a Kubernetes resource for which an event is
  emitted. The target resource is typically managed by the stack which
  has hooks listening to the events for the target resource.
* An **event** is a signal raised when a change happens to a target
  resource. The event indicates the type and details of the change.
* A **trigger** is a set of glue logic to call a hook when an event
  occurs. It consists of a hook and a filter.
* An event may **trip** a trigger.
* A **hook** is some set of custom logic which is invoked by a trigger
  in response to an event. For example, a hook may manage a deployment
  rollout, or may upgrade a schema's database.
* A trigger may be configured with a **filter** so that it only calls
  its hook if the event matches certain criteria.

## Events

At its simplest, a hook has two parts:

* A trigger
* Logic that runs

An example of a trigger is something which watches an event. For
example, perhaps a trigger will trip whenever a certain Kind of resource
is created (the **target resource**).

### Supported events

The expected events that will be supported on an individual target
resource of a recognized CRD for the stack.

* preCreate
* postCreate
* preUpdate
* postUpdate
* preDelete
* postDelete

Other events which are also under consideration include the following,
which are all events scoped to the stack itself:

* postInstall
* preUninstall
* preUpgrade
* postUpgrade

#### Inputs for events

When events involve a resource being **changed**, the input for the hook
would include a copy of the object before the change (the "old" object),
and another copy of the object after the change (the "new" object).

Other operations, such as create and delete, would only include one copy
of the affected resource.

The other inputs for a hook could include all of the resources created
as a result of the target resource having been created. For example, if
a WordpressInstance "W" was the target resource, let us assume that a
database "D" and a connection secret "S" were also created in response
to "W" originally having been created. A hook watching events on "W"
would also receive references to "D" and "S" as part of its input set.
If a hook wanted to do a database schema upgrade, it would need the
connection string information.

#### Pre- vs Post- events

In general, `preXXX` events would be triggered before a change is
committed. The hook would be able to modify the object before it is
written, and could reject the change. This is similar to the behavior of
[Admission Webhooks][admission-webhook].

In general, `postXXX` events would be triggered after a change is
committed. The objects passed to the hook should be considered
immutable. This is similar to the behavior of the [shared informer
controller][shared-informer-controller] in the client-go library.

### Event Filters

A more sophisticated trigger may also include a **filter**, which
requires a certain condition to be true for the trigger to trip, even if
an event occurred. For example, maybe a resource is created, which could
cause a trigger to trip, but the trigger has a filter specifying that
the name of the resource must be `triggeringname`. If the resource had a
name of `coolname`, the trigger would not trip.

Filters could theoretically be contained in the logic portion of a hook,
instead of in the trigger portion. Filters in the trigger portion are
considered a convenience for the author of the logic portion.

## Logic

A good place to start with implementing the logic would be to allow the
author to create a [Job][kubernetes-job]. The contract for providing
inputs would be that they would be mounted as volumes for the job
container, The author of the hook would specify the container to run for
the job.

The examples in this folder show what that contract might look like from
the perspective of the hook author.

For reference, there are many other ways that other libraries have
approached the contract. Some use templates, some webhooks written in
any language, and some simply help with writing a full-fledged go
controller.

The reason to start with a Job is that is provides the same essential
qualities as a standard event hook: a clear input contract and one-time
code execution.

<!-- Links -->
[admission-webhook]: https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/
[shared-informer-controller]: https://github.com/kubernetes/client-go/blob/master/tools/cache/controller.go
[kubernetes-job]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
