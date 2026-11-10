## 12. Final assignment

The assignment details is depicted as follow:

- `assignment-details` folder contains 2 files
  - `docker-compose.yaml` contains the docker services and routing roules
  - `assignment-details.md` contains the details to write the Kubernetes resources and deploy the `docker-compose.yaml` services in kunernetrs cluster.
- `conf` folder
  - `nginx.conf` contains the routing rules
    data
- `data` folder contains the initialization data for each database involved (`candidate-init.js` and `job-init.js`)

The assignment solution is located in solution folder.

```shell
# make sure metrics-server is up and running
cd sec10-auto-scale/resources
kubectl apply -f metrics-server.yaml
# check if metric server is working properly
kubectl top nodes
```

```shell
#
cd sec12-assignment/solution

# apply all resources
kubectl apply -f .

# now we can test everything in the browser by accessing localhost
```
