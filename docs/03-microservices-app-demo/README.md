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

## 2. Syncync the application
```
argocd app sync sox-shop
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
