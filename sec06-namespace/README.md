## Section 7: Probes

A logical partition of Kubernetes resources.

### 7.1. Namespace demo

```shell
# get all namespaces - (ns for shortcut)
kubectl get namespace
kubectl get ns

# create a namespace
kubectl create ns dev
kubectl create ns qa

# get pod in qa namespace
kubectl get pod -n qa

# create ressource in a specific namespace
kubectl apply -f . -n qa
```

### 7.2. Deployment in namespace demo

```shell
cd sec05-service

# we use v1 in this demo
kubectl apply -f 06-rolling-update-demo.yaml -n dev
kubectl get all -n dev
kubectl port-forward svc/my-app 8080:80 -n dev

# we use v2 in this demo
kubectl apply -f 06-rolling-update-demo.yaml -n qa
kubectl get all -n qa
kubectl port-forward svc/my-app 8080:80 -n qa


#
kubectl delete ns dev
kubectl delete ns qa

```
