# Labels and Node Selectors

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

## Node Selectors

First of all what is `nodeSelector`?  It is a simple Pod scheduling feature enabling Pods to be scheduled onto a node having _labels_ that match the _nodeSelector_ labels specified by the administrator.

As such `nodeSelector` is an earlier (more primitive) feature provided by Kubernetes to allow administrators to manually determine how their Pods schedule across the cluster nodes.  The general principle is that Pods are scheduled only to those nodes having matching labels as defined in the `nodeSelector`.  (Remember that `labels` are key-value pairs also defined by the administrator).

### How do nodeSelectors work?

Applying a `nodeSelector` to a Pod requires a series of steps.

1. Get the name of the Nodes and their labels.

   ```shell
   kubectl get nodes --show-labels
   ```

1. Decide which node(s) you want to assign a `key-value` label to.

   ```text
   kubectl label nodes <node-name> <key>=<value>
   ```

   For example:

   ```shell
   kubectl label nodes node02 disktype=ssd
   ```

1. Confirm that the new label has been added to the node.

   ```shell
   kubectl get nodes --show-labels
   ```

   An alternative way to see the labels defined against a node you can also run this:

   ```text
   kubectl describe node "<node name>"
   ```

Be aware that Nodes can multiple user-defined labels along with several default labels applied directly by Kubernetes, some of the more commonly seen are:

- `kubernetes.io/hostname`
- `failure-domain.beta.kubernetes.io/zone`
- `failure-domain.beta.kubernetes.io/region`
- `beta.kubernetes.io/instance-type`
- `beta.kubernetes.io/os`
- `beta.kubernetes.io/arch`

### How to specify a `nodeSelector` in a manifest file

An example manifest file that you could define might look like this:

1. Create the manifest file.

    ```shell
    cat << 'EOF' > ~/httpd-test-pod.yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: httpd
      labels:
        env: prod
    spec:
      containers:
      - name: httpd
        image: httpd
        imagePullPolicy: IfNotPresent
      nodeSelector:
        disktype: ssd
    EOF

    ```

    > **Note:** The label added has the following key:value - `disktype: ssd`.  As mentioned previously the values used are entirely user defined.

1. Run the manifest file to deploy the Pod.

   ```shell
   kubectl create -f httpd-test-pod.yaml
   ```

1. Confirm that the Pod scheduled to a node with a matching label.

   ```shell
   kubectl get pods -o wide
   ```

**Note:** As mentioned previously `nodeSelector` is an early attempt at providing a scheduling mechanism providing administrators with a means to control how pods schedule across the cluster. The preferred way to achieve this control now is to use Node Affinity instead of `nodeSelector`, and `nodeSelector` is likely to be deprecated in future releases.


## Summary

That's it.  Hopefully this has outlined what both Labels and Node Selectors are and how they work.

#### Sources

The following is an incomplete list of resources that have helped me collate/build this document:
