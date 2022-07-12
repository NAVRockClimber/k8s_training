# Demo 5 - Complex Deployment

- change to cluster due to in Rancher we don't have a storageclass (driver for volumes)
- we can tell kubectl to use different cluster
- clusters are defined in .kube/config

```Powershell
code $env:USERPROFILE\.kube\config
```

show cluster, user and connection of both in contexts
select context to switch servers
data is provided from cluster admin

```Powershell
Kubectl config get-contexts
kubectl config use-context cluster
Kubectl config get-contexts
kubectl get nodes
kubectl get namespaces
```

Show many namespaces
lets organize our stuff in namespaces

Create NS file:

```YAML
apiVersion: v1
kind: Namespace
metadata:
  name: nginx
```

Add Namespace to Deployment & Service

```YAML
  namespace: nginx
```

show NGINX web site

```Powershell
kubectl -n nginx port-forward services/nginx 8080
```

open url in browser
adjust website to greet "K8S"

## Mounts

we have multiple ways to mount files: Mounting Volume, ConfigMaps or Secret
mount pvc and palce a own html file
prepared file: https://github.com/NAVRockClimber/k8s_training/blob/main/Demo_4/index.html

create a pvc

```YAML
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nginx
  namespace: nginx
spec:
  storageClassName: azurefile-csi
  resources:
    requests:
      storage: 10Mi
  volumeMode: Filesystem
```

storageClassName:
a random name for a persistent volume could also be slow or fast
in this case csi is a driver for azure fileshare: https://portal.azure.com/#@cosmoconsult.com/resource/subscriptions/f4136c3f-1e55-4ae5-af8d-1ae3d0a25141/resourceGroups/mc_ppi-demo-aks_cluster_westeurope/providers/Microsoft.Storage/storageAccounts/feb453d7699e6485eb96da0/fileList


ReadWriteOnce:
the volume can be mounted as read-write by a single node. ReadWriteOnce access mode still can allow multiple pods to access the volume when the pods are running on the same node.

## Adding PVC to pod

```YAML
...
      volumes:
        - name: nginx-volume
          persistentVolumeClaim:
            claimName: nginx
```

```YAML
        volumeMounts:
          - name: nginx-volume
            mountPath: /usr/share/nginx/html
```

highlight relation between volumeMounts-name and volume-name
Problem, the volume does not exist yet
it is created on using it the first time
so we cannot copy the file there

## Adding a initcontainer

remember that a pod can hold multiple container
one of the is a initcontainer that intializes something and stops
after initcontainer is stopped the main container will start
got a prepared html file here: https://github.com/NAVRockClimber/k8s_training/blob/main/Demo_4/index.html
in our initcontainer we just download this to out pvc

```YAML
      ...
      initContainers:
      - name: copy-html
        image: nginx:1.23.0
        command: ["sh", "-c", "curl https://raw.githubusercontent.com/NAVRockClimber/k8s_training/main/Demo_4/index.html -o /usr/share/nginx/html/index.html"]
        volumeMounts:
          - mountPath: /usr/share/nginx/html
            name: nginx-volume
```

```
kubectl apply -f .\pvc.yaml
kubectl apply -f .\nginx-deployment.yaml
kubectl apply -f .\nginx-service.yaml
kubectl -n nginx get pods
Kubectl -n nginx port-forward services/nginx-service 8080
```

show new pvc in azure portal: https://portal.azure.com/#@cosmoconsult.com/resource/subscriptions/f4136c3f-1e55-4ae5-af8d-1ae3d0a25141/resourceGroups/mc_ppi-demo-aks_cluster_westeurope/providers/Microsoft.Storage/storageAccounts/feb453d7699e6485eb96da0/fileList

open http://localhost:8080/
