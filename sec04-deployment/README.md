## Section 4: Deployment

```shell
cd sec04-deployment
```

##### 4.1. Deployment demo

```shell
# create a deployment with 1 replicaset of a Pod with a container of Nginx
kubectl create -f 01-simple-deploy.yaml
# get information about the deployment
kubectl get deploy
```

##### 4.2. kubectl create vs kubectl apply

While `kubectl create` only create resources, `kubectl apply` can both create or update resources.

```shell
kubectl apply -f 01-simple-deploy.yaml
```

##### 4.3. Port-forward

Mapping host port with a specific resource running inside the Kubernetes cluster.

```shell
kubectl port-forward deploy/my-deploy 8080:80
```

##### 4.4. Deployment revisions

This demo use 3 revisions: vinsdocker/k8s-app:v1, vinsdocker/k8s-app:v2 and vinsdocker/k8s-app:v3

```shell
# we first create a deployment with 2 replicaset for  vinsdocker/k8s-app:v1
kubectl create -f 02-deploy-rs.yaml
# we them update the image version to v2, re-apply and observe the change
kubectl apply -f 02-deploy-rs.yaml
# we lastly update the image version to v3, re-apply and observe the change
kubectl apply -f 02-deploy-rs.yaml
```

##### 4.5. Rollback to a specific version

To better track revision we can use:

```yaml
metadata:
  annotations:
    kubernetes.io/change-cause: "raison of the change"
```

```shell
# for this deployment
# let's set it to "deploying v1" and use vinsdocker/k8s-app:v1 image.
kubectl create -f 03-deploy-rollout.yaml
# for this deployment
#let's set it to "deploying v2" and use vinsdocker/k8s-app:v2 image.
kubectl apply -f 03-deploy-rollout.yaml
#for this deployment
#let's set it to "deploying v3" and use vinsdocker/k8s-app:v3 image.
kubectl apply -f 03-deploy-rollout.yaml
```

we can rollback to any version like this

```shell
# rollback to v2 + check the history
kubectl rollout undo deploy/order-service-deploy --to-revision=2
kubectl rollout history deploy/order-service-deploy --revision=2

# rollback to v1 + check the history
kubectl rollout undo deploy/order-service-deploy --to-revision=1
kubectl rollout history deploy/order-service-deploy --revision=1

# or rollback to v3
kubectl rollout undo deploy/order-service-deploy --to-revision=3

kubectl delete -f 03-deploy-rollout.yaml
```

Note: we set `minReadySeconds` to allow a short period the Pod to run, see setup in file `04-min-ready-seconds.yaml`

##### 4.6. Deployment strategy: Recreate and RollingUpdate

- Recreate: guarantees that 2 differents versions of an application never run simultaneously. (see the previous example with rollback to different version)
- RollingUpdate: prioritize continiuous uptime.

##### 4.7. Recreate demo

```shell
# we deploy first vinsdocker/k8s-app:v1
kubectl apply -f 05-deploy-recreate.yaml

# we update the image to v2 and apply
# this have effect after executing this command to remove the previous deployment and apply the new one, here downtime is expected
kubectl apply -f 05-deploy-recreate.yaml

#
kubectl delete -f 05-deploy-recreate.yaml
```

##### 4.8. RollingUpdate demo

- rolling update with `maxSurge`

```shell
# we deploy first vinsdocker/k8s-app:v1
kubectl apply -f 06-deploy-max-surge.yaml

# we update the version to v2 and maxSurge: "100%"
# this have effect after executing this command, to add 3 replicas of v2 and when they are ready only them all the Pods with v1 versions are terminated.
kubectl apply -f 06-deploy-max-surge.yaml

# we update the version to v3 and maxSurge: "1"
# this have effect after executing this command, to have an additional one v3 version running and once ready terminate one a Pod with v2 version and repeat the process until all Pod with v2 upgraded to v3
kubectl apply -f 06-deploy-max-surge.yaml

#
kubectl delete -f 06-deploy-max-surge.yaml

```

- rolling update with `maxUnavailable`
  `maxUnavailable` dictates the maximum number of Pods that can be offline or unhealthy during the rollout process. For this demo `maxUnavailable` is set to `1`

```shell
# we deploy first vinsdocker/k8s-app:v1
# maxUnavailable: 1 (for all the demo)
kubectl apply -f 7-deploy-max-unavailable.yaml
# we update to v2 and apply
kubectl apply -f 7-deploy-max-unavailable.yaml
# we update to v3 and apply
kubectl apply -f 7-deploy-max-unavailable.yaml

#
kubectl delete -f 7-deploy-max-unavailable.yaml

```
