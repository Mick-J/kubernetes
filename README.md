# kubernetes

Kubernetes, often abbreviated as **K8s**, is an open-source platform for automating the deployment, scaling, networking, and management of containerized applications.

This is our journey of learning kubernetes using `Kind` and `kubectl` and `docker`.
For `docker` we installed it [here](https://www.docker.com/products/docker-desktop/)
We followed instructions and installed `kind` and `kubectl` using kubernetes website [here](https://kubernetes.io/docs/setup/learning-environment/)

For most learning process, we will open 2 terminal instances.

- one for k8s commands
- the second to watch creation of k8s resources (pods, replicas, deployments, services, etc.) by using the command:

```shell
watch -t -x kubectl get all
```

### References

- [Kubernetes website](https://kubernetes.io/docs/home/)
- [TechWorld with Nana - youtube](https://www.youtube.com/watch?v=s_o8dwzRlu4)
- [Udemy kubernetes course](https://www.udemy.com/course/kubernetes-cloud-native/)
