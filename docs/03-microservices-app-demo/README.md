# Sox Shop Microservices Demo App Deployment

## 1. Install the application

```
kubectl create namespace sox-shop

argocd app create sox-shop \ 
--repo https://github.com/microservices-demo/microservices-demo \
--path deploy/kubernetes/helm-chart \
--dest-server https://kubernetes.default.svc \
--dest-namespace sox-shop
```

Check the result:

```
$ argocd app list
NAME             CLUSTER                         NAMESPACE  PROJECT  STATUS     HEALTH   SYNCPOLICY  CONDITIONS
sox-shop         https://kubernetes.default.svc  sox-shop   default  OutOfSync  Missing  <none>      <none>
```

## 2. Syncync the application
```
argocd app sync sox-shop
```

Check the result:
```
$ argocd app list
NAME             CLUSTER                         NAMESPACE  PROJECT  STATUS  HEALTH       SYNCPOLICY  CONDITIONS
sox-shop         https://kubernetes.default.svc  sox-shop   default  Synced  Progressing  <none>      <none>
```

## 3. Checking the application
```
argocd app get sox-shop
```

## 4. Deleting the application
```
argocd app delete sox-shop
kubectl delete namespace sox-shop
```

## 5. Accidentally deleting the application

```
$ kubectl delete namespace sox-shop
namespace "sox-shop" deleted

$ argocd app list
NAME             CLUSTER                         NAMESPACE  PROJECT  STATUS     HEALTH   SYNCPOLICY  CONDITIONS
sox-shop         https://kubernetes.default.svc  sox-shop   default  OutOfSync  Missing  <none>      <none>

$ argocd app get sox-shop
Name:               sox-shop
Project:            default
Server:             https://kubernetes.default.svc
Namespace:          sox-shop
URL:                https://localhost:8080/applications/sox-shop
Repo:               https://github.com/microservices-demo/microservices-demo
Target:
Path:               deploy/kubernetes/helm-chart
Sync Policy:        <none>
Sync Status:        OutOfSync from  (c0a1e59)
Health Status:      Missing

GROUP       KIND        NAMESPACE  NAME                                    STATUS     HEALTH
extensions  Deployment  sox-shop   catalogue                               OutOfSync  Missing
extensions  Deployment  sox-shop   front-end                               OutOfSync  Missing
extensions  Deployment  sox-shop   orders-db                               OutOfSync  Missing
```

Wiping off from the argocd:

```
$ argocd app delete sox-shop
$ argocd app list
NAME             CLUSTER                         NAMESPACE  PROJECT  STATUS  HEALTH   SYNCPOLICY  CONDITIONS
$
```

[Back](./../../README.md)
