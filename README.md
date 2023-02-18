# Helm

## Lab 2

---
```bash

controlplane $ ls
python_app  redis
controlplane $ helm install redis ./redis/
NAME: redis
LAST DEPLOYED: Sat Feb 18 19:51:48 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=redis,app.kubernetes.io/instance=redis" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT
```

```bash
controlplane $ helm install redis ./python_app/
Error: cannot re-use a name that is still in use
controlplane $ helm install app ./python_app/
NAME: app
LAST DEPLOYED: Sat Feb 18 19:52:01 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services app-python-chart2)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
```

```bash
controlplane $ kubectl get pods
NAME                                 READY   STATUS    RESTARTS   AGE
app-python-chart2-5ddfc877d9-glhtg   1/1     Running   0          7s
app-python-chart2-5ddfc877d9-hlcv2   1/1     Running   0          7s
redis-59455f697c-fssj2               1/1     Running   0          19s
redis-59455f697c-rw96w               1/1     Running   0          19s
```

```bash
controlplane $ kubectl get services
NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
app-python-chart2-service   NodePort    10.102.232.1    <none>        80:30009/TCP   94s
kubernetes                  ClusterIP   10.96.0.1       <none>        443/TCP        23d
redis                       ClusterIP   10.101.184.70   <none>        6379/TCP       106s
```


```bash
controlplane $ curl 10.102.232.1
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<link rel="stylesheet" type="text/css" href="/static/css/style.css?v=6b28786ce3f3ad7b9bd1fdcc0889c967">
<title>Hello, world!</title>
</head>
<body>
<div>
<p>
👋 Hi from
<span class="highlight" id="DEV">DEV</span>
environment!
</p>
<p><span class="counter">Counter:</span> 1</p>
</div>
<div>
<button type="button" onClick="window.location.reload()">Reload</button>
</div>
</body>
</html>
controlplane $ 
```
