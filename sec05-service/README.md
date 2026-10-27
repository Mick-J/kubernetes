## Section 5: Service

```shell
cd sec05-service
```

### 5.1. Service demo

```shell
# apply both the deployment and the service
# here the service (named nginx) provide stable virtual endpoints to targeted pods with labels my-app
# note: we can not access pods from outside the cluster with this configuration.
kubectl apply -f 01-simple-deploy.yaml
kubectl apply -f 02-simple-service.yaml

#
kubectl delete -f 02-simple-service.yaml
```

### 5.2. Demo access pods via service

```shell
# we have a front-end pod facing a service which distribute randomly load to the targeted Pods
kubectl apply -f 03-svc-load-balancing.yaml
# access in bash mode to the front-end pod
kubectl exec -it demo-pod -- bash
# executing many time curl will randomly return response from any random of the targeted pods
~ curl my-app
~ curl my-app
~ curl my-app

#
exit
#
kubectl delete -f 02-simple-service.yaml
```

### 5.3. Demo NodePort

Instead of using a front-end facing pod, we make use of NodePort and map the service nodePort to the host port. Here is a demo:

```shell
kubectl apply -f 05-nodeport-service.yaml
curl localhost:30001

#
kubectl delete -f 05-nodeport-service.yaml
```

### 5.4. App assignment

In this assignment we have 2 deployments:

- k8s-deploy-assignment that deploy 1 pod containing an app
- one redis deployment
- and a service facing the redis deployment.

```shell
kubectl apply -f 04-app-assignment.yaml
kubectl port-forward deploy/assignment 8080:80
#
kubectl delete -f 04-app-assignment.yaml
```

### 5.5. Rolling update with service

```shell
# we start with v1 and while the loop is still on, we modify the image to v2 and re-apply
# we can observe that the transition from v1 to v2 (a mix of v1 and v2 for a while) and after a certain time we have only v2 running and all v1 terminated
kubectl apply -f 06-rolling-update-demo.yaml
#
for i in {1..1000}; do curl -s http://my-app | grep -o "<title>[^<]*" | tail -c+8; done

#
kubectl delete -f 06-rolling-update-demo.yaml

```
