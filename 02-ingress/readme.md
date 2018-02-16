+ 1. Deploy two node cluster
+ 2. Apply RBAC
```
kubectl create clusterrolebinding permissive-binding \
  --clusterrole=cluster-admin \
  --user=admin \
  --user=kubelet \
  --group=system:serviceaccounts

```
+ 3. Deploy yaml files

```
kubectl apply -f https://raw.githubusercontent.com/lukasmacko/mycontivtest/master/02-ingress/1-default-backend.yaml
kubectl apply -f https://raw.githubusercontent.com/lukasmacko/mycontivtest/master/02-ingress/2-ingress-controller.yaml
kubectl apply -f https://raw.githubusercontent.com/lukasmacko/mycontivtest/master/02-ingress/3-ingress-service.yaml

```

+ 3.a (optional) if the pods are not deployed on worker you'll have to add
```
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: ingress-nginx
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: ingress-nginx
    spec:
      nodeSelector:
        kubernetes.io/hostname: WORKER

```

+ 4. Verify that pods are deployed on worker
```
kubectl get pods -o wide --all-namespaces

```

```
$ kubectl get pods -o wide --all-namespaces
NAMESPACE     NAME                                    READY     STATUS              RESTARTS   AGE       IP             NODE
default       ingress-nginx-5bf6856495-dx2wh          1/1       Running             0          34s       10.1.1.3       slave-virtualbox
default       nginx-64cb8b7dd6-w69jt                  1/1       Running             0          28s       10.1.1.4       slave-virtualbox
default       nginx-default-backend-6bc86bb88-kqmxj   1/1       Running             0          43s       10.1.1.2       slave-virtualbox

```

+ 5. Find service IP if ingress-nginx

```
kubectl get svc
```
```
$ kubectl get svc
NAME                    TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx           LoadBalancer   10.106.184.161   <pending>     80:31217/TCP,443:30187/TCP   22s
kubernetes              ClusterIP      10.96.0.1        <none>        443/TCP                      2m
nginx                   ClusterIP      10.111.183.161   <none>        80/TCP                       18s
nginx-default-backend   ClusterIP      10.97.78.165     <none>        80/TCP                       26s

```

6. Verify from both nodes. Expected output is the same

```
$ curl 10.106.184.161
default backend - 404
```

```
$ curl -H 'Host:mysite.com' 10.106.184.161
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
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


