# Module 2 - Deploy an app

The common format of a `kubectl` command is: kubectl action resource.

## Useful commands:

### Check that kubectl is configured to talk to defined cluster
```shell
$ kubectl version
```

### View the nodes in the cluster
```shell
$ kubectl get nodes
```

### Deploy first app
```shell
$ kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1
```
This performed a few things:
* searched for a suitable node where an instance of the application could be run (there is only 1 available node)
* scheduled the application to run on that Node
* configured the cluster to reschedule the instance on a new Node when needed

### To list deployments
```shell
$ kubectl get deployments
```

### Run proxy to set a connection between host and the cluster
```shell
$ echo -e "\n\n\n\e[92mStarting Proxy. After starting it will not output a response. Please click the first Terminal Tab\n"; 
$ kubectl proxy
```

### Query the version directly through the API 
```shell
$ curl http://localhost:8001/version
```

### Get the pod name
```shell
$ export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
$ echo Name of the Pod: $POD_NAME
```

### Access the pod through an API
```shell
$ curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/
```
In order for the new deployment to be accessible without using the Proxy, a Service is required.
