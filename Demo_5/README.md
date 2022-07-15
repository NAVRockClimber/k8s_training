# Demo 5 - Complex Deployment

Now let us use a config map to store the url
configmap can be used as volume or environment variable

## create configmap

configmap.yaml:

```YAML
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx
  namespace: nginx
data:
  html-url: https://raw.githubusercontent.com/NAVRockClimber/k8s_training/main/Demo_4/index.html
```

## update deployment

add volume

```YAML
      volumes:
        - name: nginx-configmap
          configMap:
            name: nginx
```

add environment

```YAML
        env:
          - name: htmlurl
            valueFrom:
              configMapKeyRef:
                name: nginx-configmap
                key: html-url
```

update command

```YAML
        command: ["sh", "-c", "curl $htmlurl -o /usr/share/nginx/html/index.html"]
```

## apply

```Powershell
kubectl apply -f .\configmap.yaml
kubectl apply -f .\nginx-deployment.yaml
kubectl -n nginx get pods
```

## secrets

the same works for secrets due to they are the same just base64 obfusicated
Secrets should not be commited into a git repository

## create secrets


Prepared secret html: https://github.com/NAVRockClimber/k8s_training/blob/main/Demo_5/index.html


```Powershell
kubectl -n nginx create secret generic secreturl --from-literal html-url=https://raw.githubusercontent.com/NAVRockClimber/k8s_training/main/Demo_5/index.html
kubectl -n nginx create secret generic secreturl --from-literal html-url=https://raw.githubusercontent.com/NAVRockClimber/k8s_training/main/Demo_5/index.html --dry-run -o yaml
```

Update deployment to
```YAML
          - name: htmlurl
            valueFrom:
              secretKeyRef:
                name: secreturl
                key: html-url
```

```Powershell
kubectl apply -f .\nginx-deployment.yaml
Kubectl -n nginx port-forward services/nginx-service 8080
```

http://localhost:8080/