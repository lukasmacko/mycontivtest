1. Deploy two node cluster
2. Deploy testing pod on worker
```
kubectl run something --image=lmacko1992/myping
```

```
$ kubectl get pods -o wide
NAME                         READY     STATUS             RESTARTS   AGE       IP         NODE
something-767ffd5cd4-gjxqs   1/1       Running            0          2h        10.1.2.2   slave-virtualbox

```
3. Exec bash in the testing pod. Must be run on worker
```
slave@slave-VirtualBox:~$ docker ps | grep myping
52828f0f4d77        lmacko1992/myping@sha256:4dd66ad428c82bc513008cd90105af14f8b58cade8fa579d0764384b26a3f9f6                           "sh -c 'tail -f /d..."   About an hour ago   Up About an hour                        k8s_something_something-767ffd5cd4-gjxqs_default_c0e321e1-12f2-11e8-b838-080027d08767_0

docker exec -it bash 52828f0f4d77
```
or
```
docker exec -it `docker ps | grep myping | cut -d ' ' -f 1` bash
```

4. Verify access to service using IP

```
wget 10.96.0.1:443
```

output should be like:
```
root@something-767ffd5cd4-gjxqs:/# wget 10.96.0.1:443
--2018-02-16 10:27:11--  http://10.96.0.1:443/
Connecting to 10.96.0.1:443... connected.
HTTP request sent, awaiting response... 200 No headers, assuming HTTP/0.9
Length: unspecified
Saving to: 'index.html.4'

index.html.4                                           [ <=>                                                                                                             ]       7  --.-KB/s    in 0s      

2018-02-16 10:27:11 (875 KB/s) - 'index.html.4' saved [7]

```

5. Use wget to verify acces to kubernetes.default service:
```
wget kubernetes.default:443

```
out should be like
```
root@something-767ffd5cd4-gjxqs:/# wget kubernetes.default:443
--2018-02-16 10:25:22--  http://kubernetes.default:443/
Resolving kubernetes.default (kubernetes.default)... 10.96.0.1
Connecting to kubernetes.default (kubernetes.default)|10.96.0.1|:443... connected.
HTTP request sent, awaiting response... 200 No headers, assuming HTTP/0.9
Length: unspecified
Saving to: 'index.html.3'

index.html.3                                           [ <=>                                                                                                             ]       7  --.-KB/s    in 0s      

2018-02-16 10:25:22 (952 KB/s) - 'index.html.3' saved [7]

```


