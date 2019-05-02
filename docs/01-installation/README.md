# ArgoCD Installation

## 1. Install Argo CD client

### macOS

```bash
brew tap argoproj/tap
brew install argoproj/tap/argocd
```

### Linux

```bash
ARGO_CD_LATEST=$(curl --silent "https://api.github.com/repos/argoproj/argo-cd/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/${ARGO_CD_LATEST}/argocd-linux-amd64
chmod +x /usr/local/bin/argocd
```

## 2. Install Argo CD server components

```bash
kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```


## 3. Change the default admin password

Expose the argocd's port - in a second terminal input:

```bash
kubectl port-forward -n argocd service/argocd-server 8080:443
```

Pass changing:
```bash
argocd_default_pass=$(kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2)
argocd_new_pass=newpass
argocd login --username=admin --password="${argocd_default_pass}" localhost:8080
argocd account update-password --current-password="${argocd_default_pass}" --new-password="${argocd_new_pass}"
argocd relogin --password="${argocd_new_pass}"
```
