# Updating the app

## Performing a rolling update using `kubectl`

Similar to application Scaling, if a Deployment is exposed publicly, the Service will load-balance the traffic only to available Pods during the update. An available Pod is an instance that is available to the users of the application.

Rolling updates allow the following actions:
* Promote an application from one environment to another (via container image updates)
* Rollback to previous versions
* Continuous Integration and Continuous Delivery of applications with zero downtime

## Update the version of the app

### list deployments
```shell
$ kubectl get deployments
```

### list running pods 
```shell
$ kubectl get pods
```

### view current image version
```shell
$ kubectl describe pods
```

### update app image to `v2`
```shell
$ kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
```
The command notified the Deployment to use a different image for the app and initiated a rolling update.

### check status of the new pods
```shell
$ kubectl get pods
```

## Verify an update

### check that the app is running
```shell
$ kubectl describe services/kubernetes-bootcamp
```

### export `NODE_PORT`
```shell
$ export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
$ echo NODE_PORT=$NODE_PORT
```

### do a `curl` to the exposed IP and port
```shell
$ curl $(minikube ip):$NODE_PORT
```
Every time you run the `curl` command, you will hit a different Pod. Notice that all Pods are running the latest version (v2).

### confirm the update with `rollout status`
```shell
$ kubectl rollout status deployments/kubernetes-bootcamp
```
`--> deployment "kubernetes-bootcamp" successfully rolled out`

### view the current image version of the app
```shell
$ kubectl describe pods
```

## Rollback an update

### perform another update tagged with `v10`
```shell
$ kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v10
```

### see the status of the deployment
```shell
$ kubectl get deployments
```
Notice that the output doesn't list the desired number of available Pods.

### list all pods
```shell
$ kubectl get pods
```
Notice that some of the Pods have a status of `ImagePullBackOff`.

### get more insight
```shell
$ kubectl describe pods
```
In the `Events` section of the output for the affected Pods, notice that the `v10` image version did not exist in the repository.

### roll back the deployment to last working version
```shell
$ kubectl rollout undo deployments/kubernetes-bootcamp
```
`--> deployment.apps/kubernetes-bootcamp rolled back`

This command reverts the deployment to the previous known state (`v2` of the image). Updates are versioned and you can revert to any previously known state of a deployment.

### list the pods again
```shell
$ kubectl get pods
```
4 pods are running.

### check image deployed on the Pods
```shell
$ kubectl describe pods
```
The deployment is once again using a stable version of the app (v2). The rollback was successful.
