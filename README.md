# Installation

[doc 1](https://blog.argoproj.io/using-gitops-to-deploy-kubeflow-with-argo-cd-76f6b27807c)

## Client

### macOS

```
brew install argoproj/tap/argocd
```

### Linux

```
ARGO_CD_LATEST=$(curl --silent "https://api.github.com/repos/argoproj/argo-cd/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/${ARGO_CD_LATEST}/argocd-linux-amd64
chmod +x /usr/local/bin/argocd
```

## Server part

```
kubectl create namespace argocd

ARGO_CD_LATEST=$(curl --silent "https://api.github.com/repos/argoproj/argo-cd/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/${ARGO_CD_LATEST}/manifests/install.yaml
```
