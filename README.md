# Kubernetes
Some experiments using kind to bootstrap local kubernetes cluster.

## k3d
A cluster created with:
```
k3d cluster create --k3s-arg '--disable=traefik@server:0' --volume /tmp/kube:/tmp/kube -p 8081:80@loadbalancer --agents 2
```
Can be inspected with:
```
kubectl -n kube-system get all
```
To verify its footprint:
```
NAME                                          READY   STATUS    RESTARTS   AGE
pod/local-path-provisioner-5ff76fc89d-pjqpr   1/1     Running   0          65m
pod/metrics-server-86cbb8457f-4247f           1/1     Running   0          65m
pod/coredns-7448499f4d-x7kfq                  1/1     Running   0          65m

NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                  AGE
service/kube-dns         ClusterIP   10.43.0.10      <none>        53/UDP,53/TCP,9153/TCP   65m
service/metrics-server   ClusterIP   10.43.132.208   <none>        443/TCP                  65m

NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/local-path-provisioner   1/1     1            1           65m
deployment.apps/metrics-server           1/1     1            1           65m
deployment.apps/coredns                  1/1     1            1           65m

NAME                                                DESIRED   CURRENT   READY   AGE
replicaset.apps/local-path-provisioner-5ff76fc89d   1         1         1       65m
replicaset.apps/metrics-server-86cbb8457f           1         1         1       65m
replicaset.apps/coredns-7448499f4d                  1         1         1       65m
```

## kind
The cluster can be created with:
```
kind create cluster --config kind-config.yamlk
```
Inspected with:
```
kubectl -n kube-system get all
```
And we can check a more complex infrastructure:
```
NAME                                             READY   STATUS    RESTARTS   AGE
pod/coredns-558bd4d5db-gvwvz                     1/1     Running   0          74m
pod/coredns-558bd4d5db-t5mr5                     1/1     Running   0          74m
pod/etcd-kind-control-plane                      1/1     Running   0          74m
pod/kindnet-6khxg                                1/1     Running   0          74m
pod/kindnet-mg52c                                1/1     Running   0          74m
pod/kindnet-qtjq6                                1/1     Running   0          74m
pod/kube-apiserver-kind-control-plane            1/1     Running   0          74m
pod/kube-controller-manager-kind-control-plane   1/1     Running   1          74m
pod/kube-proxy-hhsx8                             1/1     Running   0          74m
pod/kube-proxy-m9cqf                             1/1     Running   0          74m
pod/kube-proxy-n5sfx                             1/1     Running   0          74m
pod/kube-scheduler-kind-control-plane            1/1     Running   1          74m

NAME               TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
service/kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   74m

NAME                        DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
daemonset.apps/kindnet      3         3         3       3            3           <none>                   74m
daemonset.apps/kube-proxy   3         3         3       3            3           kubernetes.io/os=linux   74m

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/coredns   2/2     2            2           74m

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/coredns-558bd4d5db   2         2         2       74m
```