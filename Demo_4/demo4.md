# Demo 4 - Complex Deployment

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


Make Service external reachable

```YAML
    type: LoadBalancer
```

open url in browser
adjust website to greet "K8S"
we unfortunately have no pv here
we replace the PV by a configmap

```YAML
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx
  namespace: nginx
data:
  index.html: |
    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to nginx!</title>
    <style>
    html { color-scheme: light dark; }
    body { width: 35em; margin: 0 auto;
    font-family: Tahoma, Verdana, Arial, sans-serif; }
    </style>
    </head>
    <body>
    <h1>Welcome to K8S</h1>
    <p>If you see this page, the nginx web server is successfully installed and
    working. Further configuration is required.</p>

    <p>For online documentation and support please refer to
    <a href="http://nginx.org/">nginx.org</a>.<br/>
    Commercial support is available at
    <a href="http://nginx.com/">nginx.com</a>.</p>

    <p><em>Thank you for using nginx.</em></p>    
    </body>
    </html>
```

mount configmap into pod

```YAML
    spec:
      containers:
      - name: nginx
        image: nginx:1.23.0
        resources:
          limits:
            memory: "25Mi"
            cpu: "50m"
        ports:
        - containerPort: 80
        volumeMounts:
          - name: nginx-configmap
            mountPath: /usr/share/nginx/html/index.html
            subPath: index.html
            readOnly: true
      volumes:
        - name: nginx-configmap
          configMap:
            name: nginx
```

show updated Website