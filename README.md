# kubernetes

Kubernetes, often abbreviated as **K8s**, is an open-source platform for automating the deployment, scaling, networking, and management of containerized applications.

This is our journey of learning kubernetes using `Kind` and `kubectl` and `docker`.
For `docker` we install it [here](https://www.docker.com/products/docker-desktop/)
We follow instruction and install `kind` and `kubectl` in kubernetes website [here](https://kubernetes.io/docs/setup/learning-environment/)

For most of this learning process, we will open 2 terminal

- one for k8s commands
- the second to watch creation of pods, replicas, deployments, services, etc... by using the command `watch -t -x kubectl get all`

## Section 1: Cluster creation

```shell
# create k8s cluster
cd sec01-cluster-creation
kind create cluster --config 01-cluster.yaml

# Delete the cluster after all demo
kind delete cluster --name dev-cluster
```

## Section 2: Pod

```shell
cd sec02-pod
```

##### 2.1. Basisc: create and delete a Pod:

```shell
# create a pod
kubectl create -f 01-simple-pod.yaml

# delete a particular pod (2 syntax)
kubectl delete pod my-pod
kubectl delete pod/my-pod

# delete pod
kubectl delete -f 01-simple-pod.yaml
```

##### 2.2. Get Pod information

```shell
# create a pod
kubectl create -f 02-failing-pod.yaml
# describe all pods
kubectl describe pod
#
kubectl delete -f 02-failing-pod.yaml
```

```shell
# list all Kubernetes Pods in the current namespace along with extended information
kubectl get pod -o wide

# fetches the complete, live configuration and state of a specific Pod named my-pod
# formatted as a YAML file.
kubectl get pod my-pod -o yaml
```

##### 2.3. multiple Pod:

```shell
#
kubectl create -f 03-muti-pod.yaml
# Get a specific pod
kubectl get pod pod-3
#
kubectl describe pod pod-3


## --Query pod(s) using labels:=--
# get all labels
kubectl get pod --show-labels

# query a particular label
kubectl get pod -l dept=dept-1
kubectl get pod -l team=team-a
# query label multi-criteria
kubectl get pod -l dept=dept-1,team=team-a

#
kubectl delete -f 03-muti-pod.yaml
```

##### 2.4. Port forward

```shell
#
kubectl create -f 04-pod-port.yaml

# creates a secure, temporary network tunnel between your local machine and a Pod
# running inside your Kubernetes cluster.

# After running the command below, use the browser and access localhost:8080
kubectl port-forward my-pod 8080:80

#
kubectl delete -f 04-pod-port.yaml
```

##### 2.5. restartPolicy setting

The restartPolicy setting in a Kubernetes Pod spec determines how Kubernetes handles containers inside the Pod when they exit or terminate.

```shell
# After this command the ubuntu inside the container does not terminate immediatly.
kubectl create -f 05-restart-policy.yaml

#
kubectl delete -f 05-restart-policy.yaml
```

##### 2.6. Docker entrypoint/cmd equivalent in k8s

| docker     | K8s     |
| ---------- | ------- |
| ENTRYPOINT | command |
| CMD        | args    |

see `06-pod-args.yaml` and `07-pod-shell-args.yaml`

##### 2.7. terminationGracePeriodSeconds setting

The `terminationGracePeriodSeconds` setting in a Kubernetes Pod spec defines the maximum amount of time (in seconds) Kubernetes gives a Pod to clean up and shut down gracefully before forcing it to stop.

see `08-pod-termination-grace-period.yaml`

```shell
#
kubectl create -f 08-pod-termination-grace-period.yaml

# Wait one second of termination Grace Period before the pod is shutdown
kubectl delete -f 08-pod-termination-grace-period.yaml
```

##### 2.8. Demo command in k8s

```shell
#
kubectl create -f 09-pod-command.yaml

kubectl delete -f 09-pod-command.yaml
```

##### 2.9. Demo command in k8s

```shell
#
kubectl create -f 10-pod-env.yaml
# check the variable setted for the pod
kubectl logs my-pod
#
kubectl delete -f 10-pod-env.yaml
```

##### 2.10. Pod with multiple containers

```shell
kubectl create -f 11-multi-containers.yaml
# bash inside one of the 2 containers (exit to quit the container shell)
kubectl exec -it my-pod -c util -- bash
#
kubectl delete -f 11-multi-containers.yaml
```
