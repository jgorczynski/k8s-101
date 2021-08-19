# Exposing the app

## k8s services
Although each Pod has a unique IP address, those IPs are not exposed outside the cluster without a Service. Services allow your applications to receive traffic. Services can be exposed in different ways by specifying a type in the ServiceSpec:

* ClusterIP (default) - Exposes the Service on an internal IP in the cluster. This type makes the Service only reachable from within the cluster.
* NodePort - Exposes the Service on the same port of each selected Node in the cluster using NAT. Makes a Service accessible from outside the cluster using `<NodeIP>:<NodePort>`. Superset of ClusterIP.
* LoadBalancer - Creates an external load balancer in the current cloud (if supported) and assigns a fixed, external IP to the Service. Superset of NodePort.
* ExternalName - Maps the Service to the contents of the `externalName` field (e.g. `foo.bar.example.com`), by returning a `CNAME` record with its value. No proxying of any kind is set up. This type requires v1.7 or higher of `kube-dns`, or CoreDNS version 0.0.8 or higher.

Additionally, note that there are some use cases with Services that involve not defining `selector` in the spec. A Service created without `selector` will also not create the corresponding Endpoints object. This allows users to manually map a Service to specific endpoints. Another possibility why there may be no selector is you are strictly using `type: ExternalName`.

## Services and labels

Services match a set of Pods using **labels and selectors**, a grouping primitive that allows logical operation on objects in Kubernetes. Labels are key/value pairs attached to objects and can be used in any number of ways:

* Designate objects for development, test, and production
* Embed version tags
* Classify an object using tags

Labels can be attached to objects at creation time or later on. They can be modified at any time. Let's expose our application now using a Service and apply some labels.

## Commands

## Create a new service
### look for existing pods
```shell  
$ kubectl get pods
```

### list current services
```shell  
$ kubectl get services
```

### To create a new service and expose it to external traffic we’ll use the expose command with NodePort as parameter (minikube does not support the LoadBalancer option yet). The Service received a unique cluster-IP, an internal port and an external-IP (the IP of the Node)
```shell  
$ kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080
$ kubectl get services
```

### find out what port was opened externally
```shell  
$ kubectl describe services/kubernetes-bootcamp
```

### create environment variable `NODE_PORT`
```shell  
$ export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
$ echo NODE_PORT=$NODE_PORT
```

### test that the app is exposed outside of the cluster -> the service is exposed
```shell  
$ curl $(minikube ip):$NODE_PORT
```

## Using labels

### display label name
```shell  
$ kubectl describe deployment
```

### use this label to query our list of Pods -> `-l` flag
```shell  
$ kubectl get pods -l app=kubernetes-bootcamp
```

### same query for services
```shell  
$ kubectl get services -l app=kubernetes-bootcamp
```

### get the name of the pod and store it in env variable
```shell  
$ export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
$ echo Name of the Pod: $POD_NAME
```

### apply a new label
```shell  
$ kubectl label pods $POD_NAME version=v1
```

### check the new label
```shell  
$ kubectl describe pods $POD_NAME
```

### query using the new label
```shell  
$ kubectl get pods -l version=v1
```

## Deleting a service

### delete Services
```shell  
$ kubectl delete service -l app=kubernetes-bootcamp
```

### confirm that the service is gone
```shell  
$ kubectl get services
```

### confirm that route is not exposed
```shell  
$ curl $(minikube ip):$NODE_PORT
```

### confirm that the app is still running wit a curl inside the pod
```shell  
$ kubectl exec -ti $POD_NAME -- curl localhost:8080
```

We see here that the application is up. This is because the Deployment is managing the application. To shut down the application, you would need to delete the Deployment as well.
