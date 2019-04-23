# ArgoCD workshop
-----

The workshop aims to address the next questions:

* Argocd's installation & configuration
* Argocd use cases
* Usage examples
* Exercises

## Installation

[Getting started guide](https://argoproj.github.io/argo-cd/getting_started/)


### Install argocd's client

#### macOS

```
brew tap argoproj/tap
brew install argoproj/tap/argocd
```

#### Linux

```
ARGO_CD_LATEST=$(curl --silent "https://api.github.com/repos/argoproj/argo-cd/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/${ARGO_CD_LATEST}/argocd-linux-amd64
chmod +x /usr/local/bin/argocd
```

### Install argocd's server components

```
kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```


### Change the default password

Expose the argocd's port - in a second terminal input:

```
kubectl port-forward -n argocd service/argocd-server 8080:443
```

Pass changing:
```
argocd_default_pass=$(kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2)
argocd login --username=admin --password=${argocd_default_pass} localhost:8080
argocd account update-password --current-password=${argocd_default_pass} --new-password=<your_new_pass_here>
argocd relogin
```
