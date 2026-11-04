## Section 9: Volume

```shell
cd sec09-volume
```

```shell
# storageclass (or sc for short)
# display the standard kind storageclass
kubectl get sc
kubectl get storageclass
```

### 9.1. Persistent Volume Claim - PVC

PV access mode:

- ReadWriteOnce(per node)
- ReadWriteOncePod(per node)
- ReadOnlyMany
- ReadWriteMany

```shell
#
kubectl apply -f 01-simple-pvc.yaml
# retrun back my-pvc with status: Pending
kubectl get pvc
#
kubectl describe pvc
# should return: No resources found
kubectl describe pv

# do not delete the pvc, we use it for the next part
```

```shell
#
kubectl apply -f 02-pod-with-pvc.yaml
#
kubectl apply -f 02-pod-with-pvc.yaml
# now it return my-pvc with status: Bound
kubectl get pvc
#
kubectl describe pvc

# now return the persitent volume name
kubectl get pv
# we can see the node on which the volume has been created
# for our case, it is dev-cluster-worker2
# and the pv path is: /var/local-path-provisioner/pvc-c274816a-38ae-4779-8ffd-cfd4fb1b630d_default_my-pvc
kubectl describe pv

# now we can access the volume and explore it
docker exec -it dev-cluster-worker2 bash

# let create an index.html file inside the volume
cd  /var/local-path-provisioner
cd pvc-c274816a-38ae-4779-8ffd-cfd4fb1b630d_default_my-pvc
echo "Hello PVC" > index.html
# exit from the volume
exit

# now let make a port forwarding and see in the browser the content of the index.html,
# after executing the command, access in the browser: localhost:8080
# we can see the index.html
kubectl port-forward my-pod 8080:80

```

- delete persistence

```shell
# we delete the pod, but the persitence volume is still there
kubectl delete -f 02-pod-with-pvc.yaml
# the data is persistent thanks to pvc and pv
kubectl get pvc
# delete the pvc, and since the reclaim policy is DELETE, the pv is deleted as well
kubectl delete -f 01-simple-pvc.yaml
```

### 9.2. StatefulSet

StatefulSet provides stable identity and storage semantics for stateful workloads.

```shell
#
kubectl apply 03-deployment-with-pvc.yaml
kubrctl get pvc
# after executing the command, access in the browser: localhost:8080
# each nginx instance write it hostname in the index.html of the persitent volume
kubectl port-forward deploy/my-deploy 8080:80

#
kubectl delete -f 03-deployment-with-pvc.yaml
```

### 9.3. Demo stateful gradual upgrade

- with replicatset change

```shell
# replicatset at the beginning is set to 3 and nginx version is 1.14
# after executing this command, my-ss-0, my-ss-1, my-ss-2 have been created in this order
kubectl apply -f 04-simple-ss.yaml
# let update the file and set the replicatset to 2 and apply the change
# after executing this command, we can see that my-ss-2 have been removed and we only have my-ss-0, my-ss-1 left
kubectl apply -f 04-simple-ss.yaml
# let update the file and set the replicatset back to 3 and apply the change
#  now we have my-ss-0, my-ss-1, my-ss-2
kubectl apply -f 04-simple-ss.yaml
```

- with image version change

```shell
# now let update the version of nginx image from 1.14 to 1.15
# now the upgrade is made from my-ss-2 to my-ss-0
kubectl apply -f 04-simple-ss.yaml

#
kubectl delete -f 04-simple-ss.yaml

```

### 9.4. Statefulset with service

```shell
#
kubectl apply -f 05-ss-svc.yaml

# enter the Pod bash shell to send request to service which will forward to pod which is targeted  by the service
kubectl exec -it demo-pod -- bash
# execute this command couple time to see the effect them exit.
# we get a random hostname name
~ curl nginx
~ curl nginx
~ curl nginx
exit

#
kubectl delete -f 05-ss-svc.yaml

```

### 9.5. headless service

Used to send request to a particular pod managed by the service. So we need target the pod in this format: `<pod-name>.<service-name`

```shell
#
kubectl apply -f 06-ss-headless-svc.yaml
#
kubectl exec -it demo-pod -- bash
# get all the pod Ip addresses
~ nslookup nginx
# we can request a particular pod
~ nslookup my-ss-0.nginx
~ nslookup my-ss-1.nginx
#
~ curl my-ss-0.nginx
~ curl my-ss-2.nginx
#
~ exit
#
kubectl delete -f 06-ss-headless-svc.yaml

```

### 9.6. Dynamic PVC creation

Using Statefulset we are going to create PVC and PV related to each replicaset.

```shell
kubectl apply -f 07-ss-pvc.yaml

#
kubectl get pvc
kubectl get pv
# test port-forward to each individual pod: my-ss-0 and check in the browser localhost:8080 and them my-ss-2 and again localhost:8080 in the browser
kubectl port-forward pod/my-ss-0 8080:80
kubectl port-forward pod/my-ss-2 8080:80

# after executing this command, the pvc and pv created still remain
kubectl delete -f 07-ss-pvc.yaml

# check pvc and pv which still existing
kubectl get pvc
kubectl get pv

#
kubectl delete pvc --all
```

### 9.7. Assignment

For this assignment we are going to re-implement sec08-configuration assignment using statefulet and persistent storage.
See **08-solution** for the solution

```shell
kubectl apply -f 08-solution
kubectl get pvc
kubectl get pv

# wait for all resources up and running then access mongo-express: localhost:8081 in the browser
kubectl port-forward svc/mongo-express 8081:8081

# for testing, try do add, update and delete some items in the products DB

#
kubectl delete -f 08-solution
kubectl delete pvc --all
```
