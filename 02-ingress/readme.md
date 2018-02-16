- 1. Deploy two node cluster
- 2. Apply RBAC
```
kubectl create clusterrolebinding permissive-binding \
  --clusterrole=cluster-admin \
  --user=admin \
  --user=kubelet \
  --group=system:serviceaccounts

```
- 3. Deploy yaml files

```
kubectl apply -f https://raw.githubusercontent.com/lukasmacko/mycontivtest/master/02-ingress/1-default-backend.yaml
kubectl apply -f https://raw.githubusercontent.com/lukasmacko/mycontivtest/master/02-ingress/2-ingress-controller.yaml
kubectl apply -f https://raw.githubusercontent.com/lukasmacko/mycontivtest/master/02-ingress/3-ingress-service.yaml

```

- 3.a (optional) if the pods are not deployed on worker you'll have to add
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

- 4. Verify that pods are deployed on worker
```
kubectl get pods -o wide --all-namespaces

```
