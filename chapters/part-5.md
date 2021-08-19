# Scaling the app

## Overview

`Deployment` (one pod for app) -> exposing publicly via `Service`

Current objective: increased traffic -> `scaling` (changing the number of replicas in a `Deployment`)

* scaling: increasing the number of pods to the desired new state
* autoscaling: out of scope for now
* scaling to zero: termination of pods of the specified deployment

Running multiple instances of an application will require a way to distribute the traffic to all of them. Services have an integrated load-balancer that will distribute network traffic to all Pods of an exposed Deployment. Services will monitor continuously the running Pods using endpoints, to ensure the traffic is sent only to available Pods.

Advantage: ability to do **Rolling updates** without downtime.

## Commands

## Scaling a deployment

### list the deployments
```shell
$ kubectl get deployments
```
This shows:

* `NAME` lists the names of the Deployments in the cluster.
* `READY` shows the ratio of `CURRENT/DESIRED` replicas
* `UP-TO-DATE` displays the number of replicas that have been updated to achieve the desired state.
* `AVAILABLE` displays how many replicas of the application are available to your users.
* `AGE` displays the amount of time that the application has been running.

### see the ReplicaSet created by the Deployment
```shell
$ kubectl get rs
```
the name of the ReplicaSet is always formatted as `[DEPLOYMENT-NAME]-[RANDOM-STRING]`

Two important columns of this command are:

* `DESIRED` displays the desired number of replicas of the application, which you define when you create the Deployment. This is the desired state.
* `CURRENT` displays how many replicas are currently running.

### scaling the Deployment to 4 replicas
```shell
$ kubectl scale deployments/kubernetes-bootcamp --replicas=4
$ kubectl get deployments
```

### number of pods should be 4, with unique IP addresses
```shell
$ kubectl get pods -o wide
```

### display Deployments event log to investigate the change
```shell
$ kubectl describe deployments/kubernetes-bootcamp
```

## Load balancing

### check that the Service is load balancing the traffic
```shell
$ kubectl describe services/kubernetes-bootcamp
```

### store Node port in `NODE_PORT` env variable
```shell
$ export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
$ echo NODE_PORT=$NODE_PORT
```

### check exposed IP and port by `curl`ing it
```shell
$ curl $(minikube ip):$NODE_PORT
```
We hit a different Pod with every request. This demonstrates that the load-balancing is working.

## Scale down

### Let's make it 2 replicas
```shell
$ kubectl scale deployments/kubernetes-bootcamp --replicas=2
```

### list the Deployments to check if 4/4 -> 2/2
```shell
$ kubectl get deployments
```

### list the number of pods
```shell
$ kubectl get pods -o wide
```
Which confirms that 2 pods were terminated.
