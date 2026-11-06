## Section 10: Auto Scaling

### 10.1. Metric server - install

```shell
# both command return: error: Metrics API not available
kubectl top pod
kubectl top nodes
```

let install Metric server

```shell
kubectl apply -f resources/metrics-server.yaml
```

now we can get some metrics-server information

```shell
# show among information the metrics-server information
kubectl get pod -n kube-system

# retrun cpu and memory utilization
kubectl top nodes
```

### 10.2. Pod pending

```shell
# with replica set to 3
kubectl apply -f 01-deploy-cpu-memory-usage.yaml
kubectl top pods

# with replica set to 8, when we re-apply the following command, we have pod in pending state. There is no enough CPU to schedule the Pod with pending status
kubectl apply -f 01-deploy-cpu-memory-usage.yaml
```

### 10.3. HPA demo

```shell
# apply  the deployment with default 1 pod
kubectl apply -f 02-deploy-for-hpa.yaml
# apply the hpa configuration
# this hpa will scale in/scale out the number of pod
kubectl apply -f 03-hpa.yaml
# get information about the hpa
kubectl get hpa

# enter in bash node to the demo-pod launch the auto scaling test
kubectl exec -it demo-pod -- bash

# use ApacheBench to send 20000 requests with 5 concurrents requests and observe how the pods auto-scale.
# during the test we can see the scale out/in of the pods
ab -n 20000 -c 5 http://nginx/

#
kubectl delete -f 02-deploy-for-hpa.yaml
kubectl delete -f 03-hpa.yaml
```
