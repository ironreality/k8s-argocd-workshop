# Installation

[doc1](https://argoproj.github.io/argo-cd/getting_started/)

[doc2](https://blog.argoproj.io/using-gitops-to-deploy-kubeflow-with-argo-cd-76f6b27807c)

## Install the argocd client

### macOS

```
brew tap argoproj/tap
brew install argoproj/tap/argocd
```

### Linux

```
ARGO_CD_LATEST=$(curl --silent "https://api.github.com/repos/argoproj/argo-cd/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/${ARGO_CD_LATEST}/argocd-linux-amd64
chmod +x /usr/local/bin/argocd
```

## Install the argocd server

```
kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```


## Change the default argocd password:

Expose the argocd's port - in a second terminal input:

```
kubectl port-forward -n argocd service/argocd-server 8080:443
```

Pass changing:
```
argocd_pass=$(kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2)
argocd login localhost:8080
argocd account update-password
argocd relogin
```
