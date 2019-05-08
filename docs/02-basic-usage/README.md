# Argo CD basic usage

# Example. A simple Helm-templated application deployment.

As a simple application we'll use Helm-charted Nginx installation. The chart is located in [helm/nginx-test](./../../helm/nginx-test) subdirectory of the workshop's repo.

## Preparation

Connect to the argocd's API via k8s port forwarding and login into Argo CD in CLI:

```
# in a separate terminal window
kubectl port-forward -n argocd service/argocd-server 8080:443

argocd_admin_password="the_password_set_while_installation"
argocd login --username=admin --password="${argocd_admin_password}" localhost:8080
```

Create a k8s namespace to experiment within:

```
kubectl create namespace sandbox
```

## Create the application

```bash
argocd app create nginx-test --repo https://github.com/ironreality/k8s-argocd-workshop --path helm/nginx-test --dest-server https://kubernetes.default.svc --dest-namespace sandbox
```

The result in the web UI:

![ui](./pics/nginx_01.png)

## Sync the application

"Sync" in Argo CD's [terms](https://argoproj.github.io/argo-cd/core_concepts/) is the process of adjusting the application's live state toward the target state defined in the application's source repository. 

<br>
Sync the app and wait till the sync will be finished

```
argocd app sync nginx-test && argocd app wait nginx-test
```

<details>
<summary>Output</summary>

```
Name:               nginx-test
Project:            default
Server:             https://kubernetes.default.svc
Namespace:          sandbox
URL:                https://localhost:8080/applications/nginx-test
Repo:               https://github.com/ironreality/k8s-argocd-workshop
Target:
Path:               helm/nginx-test
Sync Policy:        <none>
Sync Status:        OutOfSync from  (11daaf4)
Health Status:      Missing

Operation:          Sync
Sync Revision:      11daaf4e64d4fb65e93748ce2de7563a446aff53
Phase:              Succeeded
Start:              2019-05-08 14:32:32 +0300 EEST
Finished:           2019-05-08 14:32:33 +0300 EEST
Duration:           1s
Message:            successfully synced

GROUP  KIND        NAMESPACE  NAME        STATUS     HEALTH   HOOK  MESSAGE
       Service     sandbox    nginx-test  OutOfSync  Missing        service/nginx-test created
       apps   Deployment  sandbox    nginx-test  OutOfSync  Missing        deployment.apps/nginx-test created


TIMESTAMP                  GROUP        KIND   NAMESPACE                  NAME    STATUS   HEALTH            HOOK  MESSAGE
2019-05-08T14:32:22+03:00            Service     sandbox            nginx-test    Synced  Healthy                  service/nginx-test created
2019-05-08T14:32:22+03:00   apps  Deployment     sandbox            nginx-test    Synced  Progressing              deployment.apps/nginx-test
 created

 Name:               nginx-test
 Project:            default
 Server:             https://kubernetes.default.svc
 Namespace:          sandbox
 URL:                https://localhost:8080/applications/nginx-test
 Repo:               https://github.com/ironreality/k8s-argocd-workshop
 Target:
 Path:               helm/nginx-test
 Sync Policy:        <none>
 Sync Status:        Synced to  (11daaf4)
 Health Status:      Healthy

 Operation:          Sync
 Sync Revision:      11daaf4e64d4fb65e93748ce2de7563a446aff53
 Phase:              Succeeded
 Start:              2019-05-08 14:32:32 +0300 EEST
 Finished:           2019-05-08 14:32:33 +0300 EEST
 Duration:           1s
 Message:            successfully synced

 GROUP  KIND        NAMESPACE  NAME        STATUS  HEALTH   HOOK  MESSAGE
        Service     sandbox    nginx-test  Synced  Healthy        service/nginx-test created
        apps   Deployment  sandbox    nginx-test  Synced  Healthy        deployment.apps/nginx-test created
```
</details>

<br>
Now in the web ui we can see a graph of our app's Kubernetes objects

![ui](./pics/nginx_02.png)


## Change application parameters

### Change Nginx version

```
git clone git@github.com:ironreality/k8s-argocd-workshop.git
cd k8s-argocd-workshop/helm/nginx-test

# I use yq tool to edit YAML but you can adjust the file manually
yq write --verbose --inplace values.yaml image.tag 1.15

git add values.yaml
git commit -m "example: down Nginx version"
git push origin master
```

<br>
After syncing the app in the web ui we see that Argo CD leveraged nginx-test's k8s deployment functionality - it scaled to 0 the old replica set and created a new replica set with new Nginx version.

![ui](./pics/nginx_03.png)

<br>
We can check the Nginx version right now in the deployment's manifest window

![ui](./pics/nginx_04.png)

If we turn back the nginx's version Argo CD will scale up the old nginx-test deployment's replica set

```
yq write  --inplace values.yaml image.tag 1.16
git add values.yaml
git commit -m "example: up nginx version"
git push origin master
argocd app sync nginx-test && argocd app wait nginx-test<Paste>
```

![ui](./pics/nginx_05.png)


## Checking k8s events
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

[Back](./../../README.md)
