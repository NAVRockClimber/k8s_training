## ConfigMaps & Secrets 

ConfigMaps & Secrets can be passed into a container as file or environment variable

Secrets are just a base64 encrypted configMap
Secrets should not be commited into a git repository

## Secrets as file

Create file: MySecret.txt
Content: SuperSecretStuff

```Powershell
kubectl -n nginx create secret generic mysecret --from-file=MySecret.txt
```

show secret and decode secret
ctrl+shift+4

```bash
kubectl -n nginx get secrets mysecret -o yaml
echo -n "TXlTdXBlclNlY3JldENvbnRlbnQ=" | base64 -d
```

show secret file in container

```Powershell
kubectl -n nginx exec -it <podname> -- /bin/bash
ls /usr/share/nginx/html/
cat /usr/share/nginx/html/MySecret.txt && echo
```

## Secret & Configmap as Environment Vars

new file myconfigMap.yaml

```YAML
apiVersion: v1
kind: ConfigMap
metadata:
  name: myconfigmap
  namespace: nginx
data:
  unencrypted: my-value
```

adjust deployment

```YAML
        ports:
        - containerPort: 80
        env:
          - name: configMap
            valueFrom:
              configMapKeyRef:
                name: myconfigmap
                key: unencrypted
        volumes:
        - name: nginx-configmap
          configMap:
            name: nginx
        - name: my-secret
          secret:
            secretName: mysecret
        - name: myconfigmap
          configMap:
            name: myconfigmap
```

show confimap in container

```powershell
kubectl -n nginx apply -f .\myconfigMap.yaml
kubectl -n nginx apply -f .\nginx-deployment.yaml
kubectl -n nginx exec -it nginx-76fb4766d4-rw5vx -- /bin/bash
```

```bash
echo $configMap
```

create and show secret

```powershell
kubectl -n nginx create secret generic envsecret --from-literal secretenv=myscretvalue
kubectl -n nginx get secrets envsecret
```

adjust deployment
