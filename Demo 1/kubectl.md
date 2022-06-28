# Kubectl Intro

## Demo 1.1 - deploy nginx 

``` YAML
kubectl create deployment nginx --image=nginx
``` 

## Demo 1.2 - Pods / Edit / Replicas

``` YAML
kubectl get pods
kubectl get all
```

Explain replicaset / naming of pod
Increase replicas

``` YAML
kubectl edit deployments nginx
kubectl get all
```

Explain output / replicas

## Demo 1.3 - Debug

``` YAML
Kubectl logs <name>
Kubectl describe pods <name>
``` 

Describe section events

``` YAML
kubectl exec -it [pod name] -- /bin/bash
``` 

show we are in the container

``` YAML
Kubectl delete deployments nginx
```

Cleaning Up