## 11.Ingress

Ingress provides HTTP/HTTPS routing into the cluster.
For this section we added an Ingress controller to implement the routing: an nginx-ingress controller.

```shell
# delete the old kind cluster
kind delete cluster --name dev-cluster
#
cd sec11-ingress
```

### 11.1. Ingress controller

```shell
# create the new kind cluster with ingress-nginx
kind create cluster --config 01-cluster/01-cluster.yaml

# add ingress-nginx controller to kind
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml


# check the config, we should see ingress-nginx with status Active
kubectl get ns
```

### 11.2. Ingress demo

```shell
#
kubectl apply -f 02-simple-ingress/

# get the ingress info (ing short for ingress)
kubectl get ing
```

### 11.3. Path based routing

```shell
#
kubectl apply -f 03-path-based-routing
#
kubectl get ing

# test all path in the browser
# localhost
# localhost/product
# localhost/user
# localhost/vmcefeoewmv # inexistant path, 404
```

### 11.4. Dev/QA deploy and routing

The goal of this demo is to create one deployment for qa with v1 version and one deployment for dev with v2 image version.
To achieve this we need to make use of `host + Path based routing`.
Without that configuration, Ingress-controller will have issue due to route overlapping issue. In our example:

- qa: qa.myapp.com/
- dev: dev.myapp.com/

```shell
kubectl create ns dev
kubectl create ns qa

#
kubectl apply -f 04-host-based-routing/dev-workload -n dev
kubectl apply -f 04-host-based-routing/qa-workload -n qa

#
kubectl get all -n dev
kubectl get all -n qa
```

- testing our routing

```shell
# mac/linux, edit /etc/hosts (need permission)
# add
127.0.0.1 dev.myapp.com
127.0.0.1 qa.myapp.com

# for windows edit: c:\\Windows\System32\Drivers\etc\hosts
# add
127.0.0.1 dev.myapp.com
127.0.0.1 qa.myapp.com
```

```shell
# in the browser access these links and see how ingress shift the traffic
http://dev.myapp.com
http://qa.myapp.com
```

```shell
#
kubectl delete -f 04-host-based-routing/dev-workload -n dev
kubectl delete -f 04-host-based-routing/qa-workload -n qa
#
# revert hosts file changes
```
