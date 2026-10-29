## Section 7: Probes

A probe is a **health check** diagnostic performed periodically by the Kubelet on a container.
K8s has 3 types of probes:

- startupProbe
- readinessProbe
- livenessProbe

Kubelet can be configured to check container health using one of four mechanisms:

- httpGet
- tcpSocket
- exec
- grpc

```shell
cd sec07-probe
```

### 7.1. Sartup probe demo

```shell
# demo sartup probe using httpget
kubectl apply -f 01-startup-httpget.yaml
kubectl describe pod
kubectl delete -f 01-startup-httpget.yaml

# demo sartup probe using tcpsocket
kubectl apply -f 02-startup-tcpsocket.yaml
kubectl describe pod
kubectl delete -f 02-startup-tcpsocket.yaml

# demo sartup probe using exec
kubectl apply -f 03-startup-exec.yaml
kubectl describe pod
kubectl delete -f 03-startup-exec.yaml
```

### 7.2. Liveness probe demo

```shell
#
kubectl apply -f 04-liveness-probe.yaml

# inside container remove live.html to see the behavior or liveness
# after deleting live.html, liveness probe fails for 3s and it restarts the container
kubectl exec -it my-pod -- bash
cd /usr/share/nginx/html/
ls
rm live.html
```

### 7.3. Readiness probe demo

```shell
kubectl apply -f 05-readiness-probe.yaml

# inside container remove ready.html to see the behavior or readiness
# after removing ready.html, the container is not ready to receive requests
kubectl exec -it my-pod -- bash
cd /usr/share/nginx/html/
ls
rm ready.html
# after bringing back ready.html, the container is now ready
touch ready.html

#
exit
kubectl delete -f 05-readiness-probe.yaml
```
