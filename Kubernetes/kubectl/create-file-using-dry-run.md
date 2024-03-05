# How to create and change Kubernetes YAML deployment files using dry-run and edit them

Creating YAML files can be fraught with errors if _'extra'_ spaces are included, or indentation errors are made when creating them.  The purpose of this document is to show to to use a kubectl command to create the file for you.  Below are a couple of examples showing how to do this:

## Create a basic NGINX pod YAML definition file

Use this kubectl command to run an Nginx pod on your Kubernetes (without having to pass it a pre-created deployment file).

```shell
kubectl run nginx --image=nginx
```

This will create a nginx pod on the cluster.

```text
# kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          34s
```

The problem with this approach is twofold:

- a lack of flexibility (you cannot easily add nuance to your deployment(s) using this approach)
- No record of how your application deployments were defined (making disaster recovery, or maintenance difficult)

So, instead use this kubectl command to create a _'starter'_ YAML file that can be modified to suit your needs:

```shell
kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx-pod.yaml
```

This will output a yaml file (see below) you can then apply/create or update as needed.

```text
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

## Create a basic NGINX deployment YAML definition file

The process is similar to that used to create a pod file (see above).  First of all this is how to deploy 

```shell
kubectl create deployment --image=nginx nginx
```

This creates a deployment without needing a predefined deployment YAML file.

```text
# kubectl get deployments
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           34s
```

Similar to last the approach used for pods, you can create a basic deployment YAML file using kubectl:

```shell
kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml
```

This will give you a template for creating a deployment.

```text
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {}
```

> **Note:** If you don't want, or need, a file to be created then simply omit the _'redirection'_ command from the end of these commands and the definition will only be echoed to your terminal output.  For example:

```shell
kubectl create deployment --image=nginx nginx --dry-run=client -o yaml
```

With this you can make any changes, like number of replicas or change any image name/version using your favorite edition like vim.

## Summary

So far, we have seen, how to create a new YAML file.

However, if you already have an application deployed, but no YAML definition file it is possible to make changes to a running pod directly (ie: on the fly) like this:

1. Deploy an application.

   ```text
   kubectl apply -f nginx-deployment.yaml
   deployment.apps/nginx created
   ```

1. Then directly edit the deployment yaml as follows:

   ```shell
   kubectl edit deployment/nginx
   ```

> **NOTE:** Any changes made by directly editing a running application using the `kubectl edit` method, are not applied to the YAML file used for the deployment.  Instead the change(s) are made directly to the running resource. If you wish record the changes, you should make a entry on yaml directly and use _kubectl apply_. Check the difference between _kubectl apply_ and _kubectl create_ on [https://foxutech.com/kubectl-apply-vs-kubectl-create/](https://foxutech.com/kubectl-apply-vs-kubectl-create/)
