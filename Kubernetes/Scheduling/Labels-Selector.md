# Labels and Selectors

## What are _Labels_?

Labels are applied by Kubernetes to nodes when a cluster is created.  They can also be defined, and used, by administrators to run applications on specific worker nodes, which makes them a very useful tool to learn how to use.

## How to display node labels on your Kubernetes cluster

Use this command to list pre-existing labels for all nodes in the Kubernetes cluster:

```shell
kubectl get nodes --show-labels
```

If you want to know the details for a specific node, use this:

```shell
kubectl label --list nodes < your-node-name >
```

Notice that Labels take the form of _'key-value'_ pair.  The _key_ and _pair_ elements are totally freeform, meaning they can be any values you desire. They must meet the following criteria to be valid:

- Begin with a letter, or number
- Contain letters, numbers, hyphens, dots and underscores
- Cannot be longer than 63 characters in total

## How to assign a label to a node

Suppose you want to deploy all production related nodes to a specific node (`worker-01`) in your cluster.  So let's apply a `label` to that node using a bespoke key:pair definition:

```shell
kubectl label nodes worker-01 workload=production
```

This can be confirmed as having been applied using this command:

```shell
kubectl label --list nodes worker-o1 | grep -i workload
```

> **Note:** The `grep` command is only used to return the expected label (and filter out all other results)

## How to overwrite a node label

Existing labels can be overwritten like this:

```shell
kubectl label --overwrite nodes worker-01 workload=test
```

As before, this can be confirmed like so:

```shell
kubectl label --list nodes worker-01 | grep -i test
```

## How to remove a label

Labels can be removed from any node very easily by applying a `-` to the `key` part of the label.  See below for an example:

```shell
kubectl label --overwrite nodes worker-o1 workload-
```

Again, this can be confirmed like this:

```shell
kubectl label --list worker-01 | grep -i workload
```

## Summary

That's it.  Hopefully this has outlined 