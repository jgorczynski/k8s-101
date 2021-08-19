# Explore the app

The most common operations can be done with the following `kubectl` commands:

* `$ kubectl get` - list resources
* `$ kubectl describe` - show detailed information about a resource
* `$ kubectl logs` - print the logs from a container in a pod
* `$ kubectl exec` - execute a command on a container in a pod

### Check application configuration
```shell
$ kubectl get pods
```

### View what containers are inside that Pod and what images are used to build those containers
```shell
$ kubectl describe pods
```

### Show the app in the terminal - access proxy
```shell
$ echo -e "\n\n\n\e[92mStarting Proxy. After starting it will noe first Terminal Tab\n"; kubectl proxy
```

### Get the Pod name and store it in the POD_NAME env
```shell
$ export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
$ echo Name of the Pod: $POD_NAME
```

### See the output
```shell
$ curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/
```

### Retrieve container logs
```shell
$ kubectl logs $POD_NAME
```

### List env variables by `exec`
```shell
$ kubectl exec $POD_NAME -- env
```
The name of the container itself can be omitted since there is only a single container in the Pod.

### Start a `bash` session
```shell
$ kubectl exec -ti $POD_NAME -- bash
```

### Diplay the source code of the app
```shell
$ cat server.js
```

### Check if the application is up
```shell
$ curl localhost:8080
```
*Note: here we used localhost because we executed the command inside the NodeJS Pod. If you cannot connect to localhost:8080, check to make sure you have run the kubectl exec command and are launching the command from within the Pod*

### Close container connection
```shell
$ exit
```
