# Configuration Files

Explain ports
Show in split screen: deployment <-> service
Port: which port the service publishes (internally / depens on service type)
targetPort: to which port on the pod requests are forwarded

```Powershell
kubectl apply -f .\nginx-deployment.yaml
kubectl apply -f .\nginx-service.yaml
kubectl get pods
kubectl get services
```

highlight ip (internal ip / external ip / depens on service type)
highlight port

## Show if service is connected to pod

```Powershell
kubectl describe services nginx-service
```

Highlight selector
Highlight targetport
Highlight endpoints

```Powershell
kubectl get pods -o wide
```

explain new parameter
highlight ips are the one from endpoints

**[BACK](../README.md)**
