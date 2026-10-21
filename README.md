# kubernetes

Kubernetes, often abbreviated as **K8s**, is an open-source platform for automating the deployment, scaling, networking, and management of containerized applications.

This is our journey of learning kubernetes using `Kind` and `kubectl` and `docker`.
For `docker` we install it [here](https://www.docker.com/products/docker-desktop/)
We follow instruction and install `kind` and `kubectl` in kubernetes website [here](https://kubernetes.io/docs/setup/learning-environment/)

For most of this learning process, we will open 2 terminal

- one for k8s commands
- the second to watch creation of pods, replicas, deployments, services, etc... by using the command:

```shell
`watch -t -x kubectl get all`
```
