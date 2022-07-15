## Extended ConfigMaps & Secrets 

ConfigMaps & Secrets can not only be passed as environment variables also as read-only files

## Configmap as file

reuse the index.html

create file: configMap.yaml

```YAML
apiVersion: v1
kind: ConfigMap
metadata:
  name: indexhtml
  namespace: nginx
data:
  index.html: |
    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to K8S!</title>
    <style>
    html { color-scheme: light dark; }
    body { width: 35em; margin: 0 auto;
    font-family: Tahoma, Verdana, Arial, sans-serif; }
    </style>
    Commercial support is available at
    <a href="http://nginx.com/">nginx.com</a>.</p>

    <p><em>Thank you for using nginx.</em></p>
    <p><!--#echo var="hostname"--></p>
    </body>
    </html>    </head>
    <body>
    <h1>Welcome to K8S ConfigMaps</h1>
    <p>Kubernetes rocks! ;).</p>

    <p>For online documentation and support please refer to
    <a href="http://nginx.org/">nginx.org</a>.<br/>

```

### adjust deployment


add: 

```YAML
      volumes:
        - name: indexhtml
          configMap:
            name: indexhtml
```

```YAML
        volumeMounts:
          - name: indexhtml
            mountPath: /usr/share/nginx/html
```

remove init container and env section


```Powershell
kubectl -n nginx apply -f .\configmap.yaml
kubectl -n nginx apply -f .\nginx-deployment.yaml

```

## Make it secret

Create file: index.html

```Powershell
kubectl -n nginx create secret generic indexhtml --from-file=indexhtml=index.html
```

show secret and decode secret
ctrl+shift+4

```bash
kubectl -n nginx get secrets indexhtml -o yaml
echo -n "<valu>" | base64 -d
```

adjust deplyoment

```YAML
      volumes:
        - name: indexhtml
          secret:
            secretName: indexhtml
            items:
              - key: indexhtml
                path: index.html
```

```YAML
        volumeMounts:
          - name: indexhtml
            mountPath: /usr/share/nginx/html/index.html
            subPath: index.html
```

```Powershell
kubectl apply -f .\nginx-deployment.yaml
```

show secret file in container

```Powershell
kubectl -n nginx exec -it <podname> -- /bin/bash
ls /usr/share/nginx/html/
cat /usr/share/nginx/html/index.html && echo
```

Kubectl -n nginx port-forward services/nginx-service 8080

http://localhost:8080/