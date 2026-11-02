## Section 8: Configuration

- A ConfigMap is designed to store configuration settings that do not require confidentiality.
- A Secret is explicitly built for confidential information.

```shell
cd sec08-configuration
```

### 8.1. configmap demo

```shell
kubectl apply -f 01-simple-configmap.yaml

# configmap (cm for shortcut)
kubectl get configmap
kubectl get cm

# see the detail about configuration we have set
kubectl describe cm app-properties

#
kubectl delete -f 01-simple-configmap.yaml
```

### 8.2. Inject variable vs inject file

- Inject as variable

```shell
#
kubectl apply -f 02-inject-cm-as-env.yaml
# after executing this command we can see among the pod configuration data the values of our confimap we have specified
kubectl logs my-pod

#
kubectl delete -f 02-inject-cm-as-env.yaml
```

- inject as file in the container

```shell
#
kubectl apply -f 03-inject-cm-as-file.yaml
kubectl logs my-pod
#
kubectl get cm app-properties -o yaml

#
kubectl exec -it my-pod -- bash
~ cd /usr/share/props
~ ls
# display the configuration content we have specified
~ cat application.properties
~ exit

#
kubectl delete -f 03-inject-cm-as-file.yaml
```

### 8.3. Secret - injecting environment variable

```shell
#
kubectl get secret
#
kubectl create secret generic my-secret --from-literal=username=mick --from-literal=password=admin123
#
kubectl get secret
#
kubectl get secret -o yaml
```

**Note:**
The secret value are base64 encoded. The secrets in this section are base64 encoded using the following command

```shell
#linux & macOS
echo <String-to-convert> | base64

# or for all cases, make use a docker container
docker run -it ubuntu
echo -n <String-to-convert> | base64

```

```shell
#
kubectl apply -f 04-simple-secret.yaml
#
kubectl get secret
kubectl get secret -o yaml
# after applying this command, it display the decoded value received by the pod
kubectl logs my-pod

#
kubectl delete -f 04-simple-secret.yaml
```

- inject the whole secret data as environment variable

```shell
#
kubectl apply -f  05-inject-secret-as-env.yaml
#
kubectl logs my-pod

#
kubectl delete -f 05-inject-secret-as-env.yaml
```

### 8.4. Secret - injecting file

```shell
# the secret app.key is obtain by issuing:
# cat 01-simple-configmap.yaml | base64s
kubectl apply -f 06-inject-secret-as-file.yaml
#
kubectl logs my-pod

#
kubectl exec -it my-pod -- bash
~ cd /usr/share/props
~ ls
# we can see the decoded version of the whole file we have injected in the container
~ cat app.key

#
~ exit
kubectl delete -f 06-inject-secret-as-file.yaml
```

### 8.5. Assignment

In this assignment we will use both the **docker-compose.yaml** and the **data.js** files to create corresponding kubernetes resources and deploy them on the kind cluster.

**07-assignment** contains the assignment files

- **docker-compose.yaml** file contains 2 services: mongo and and mongo-express
- **data.js** contains the bootstrap data (products collection data) for the mongo db

**08-solution** contains the assignment solution
