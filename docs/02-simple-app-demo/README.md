# Nginx Demo Deployment

## Preparation

Connect to the argocd's API:

```
# in a separate terminal window
kubectl port-forward -n argocd service/argocd-server 8080:443
```

Create a k8s namespace to experiment within:

```
kubectl create namespace sandbox
```

## Create the application

```
argocd app create nginx-test --repo https://github.com/ironreality/k8s-argocd-workshop --path helm/nginx-test --dest-server https://kubernetes.default.svc --dest-namespace sandbox
```

## Check k8s events
```
$ kubectl get events -n sandbox --sort-by='.metadata.creationTimestamp' | tail -n 20

9m1s        Normal   SuccessfulCreate    replicaset/nginx-test-6b995579dc   Created pod: nginx-test-6b995579dc-fjg65
9m1s        Normal   Scheduled           pod/nginx-test-6b995579dc-fjg65    Successfully assigned sandbox/nginx-test-6b995579dc-fjg65 to rkhanbikov-rsvrd-2
9m1s        Normal   ScalingReplicaSet   deployment/nginx-test              Scaled up replica set nginx-test-6b995579dc to 1
8m55s       Normal   Pulling             pod/nginx-test-6b995579dc-fjg65    Pulling image "nginx:1.16"
8m53s       Normal   Created             pod/nginx-test-6b995579dc-fjg65    Created container nginx-test
8m53s       Normal   Pulled              pod/nginx-test-6b995579dc-fjg65    Successfully pulled image "nginx:1.16"
8m53s       Normal   Started             pod/nginx-test-6b995579dc-fjg65    Started container nginx-test
8m54s       Normal   ScalingReplicaSet   deployment/nginx-test              Scaled up replica set nginx-test-6b995579dc to 2
8m54s       Normal   SuccessfulDelete    replicaset/nginx-test-6cfb8c99b4   Deleted pod: nginx-test-6cfb8c99b4-hcsbf
8m54s       Normal   Scheduled           pod/nginx-test-6b995579dc-k2gbs    Successfully assigned sandbox/nginx-test-6b995579dc-k2gbs to rkhanbikov-rsvrd-1
8m54s       Normal   Killing             pod/nginx-test-6cfb8c99b4-hcsbf    Stopping container nginx-test
8m54s       Normal   SuccessfulCreate    replicaset/nginx-test-6b995579dc   Created pod: nginx-test-6b995579dc-k2gbs
8m54s       Normal   ScalingReplicaSet   deployment/nginx-test              Scaled down replica set nginx-test-6cfb8c99b4 to 1
8m53s       Normal   Pulling             pod/nginx-test-6b995579dc-k2gbs    Pulling image "nginx:1.16"
8m51s       Normal   Pulled              pod/nginx-test-6b995579dc-k2gbs    Successfully pulled image "nginx:1.16"
8m51s       Normal   Created             pod/nginx-test-6b995579dc-k2gbs    Created container nginx-test
8m51s       Normal   Started             pod/nginx-test-6b995579dc-k2gbs    Started container nginx-test
8m42s       Normal   Killing             pod/nginx-test-6cfb8c99b4-g4ws4    Stopping container nginx-test
8m48s       Normal   SuccessfulDelete    replicaset/nginx-test-6cfb8c99b4   Deleted pod: nginx-test-6cfb8c99b4-g4ws4
8m48s       Normal   ScalingReplicaSet   deployment/nginx-test              Scaled down replica set nginx-test-6cfb8c99b4 to 0
```

```
kubectl get pod  -n sandbox -o go-template --template="{{range .items}}{{range .spec.containers}}{{.image}}{{println}}{{end}}{{end}}"
```
