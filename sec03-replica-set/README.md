## Section 3: ReplicaSet

```shell
#
cd se03-replica-set
```

##### 3.1. ReplicaSet creation

```shell
# this create and maintain 3 replicas of pod name "my-pod"
kubectl create -f 01-simple-rs.yaml

#
kubectl delete -f 01-simple-rs.yaml
```

##### 3.2. ReplicaSet with existing Pod

```shell
# create 3 Pods with labels "team-a"
kubectl create -f 02-multiple-pods.yaml

# maintain 2 replicat set for Pod with label that match "team-a" only
# in this example, the extra Pod will be terminated since we specify -> replicas: 2
kubectl create -f 03-existng-pod-manager.yaml
```

##### 3.3. Describe replicatSet

```shell
# create replicasets: my-rs-1 and my-rs-2
kubectl create -f 04-multiple-rs.yaml
# describe replicaset: my-rs-1
kubectl describe rs/my-rs-1
#
kubectl delete -f 04-multiple-rs.yaml
```
