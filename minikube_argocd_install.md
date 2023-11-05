# Install ArgoCD

## install ArgoCD in minikube
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

```

## Download ArgoCD CLI

```
https://github.com/argoproj/argo-cd/releases/latest

```

## Test argocd cli installation
```
argocd

VERSION=<TAG> # Select desired TAG from https://github.com/argoproj/argo-cd/releases
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/download/$VERSION/argocd-linux-amd64

-- eg: 
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/download/v2.8.6/argocd-linux-amd64

sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64


```
## access ArgoCD UI
```
kubectl get svc -n argocd
kubectl port-forward svc/argocd-server 8080:443 -n argocd

localhost:8080
```
## login with admin user and below token either using kubectl or argocd:
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode && echo

argocd admin initial-password -n argocd
```

## Delete the complete namespace

```

kubectl delete ns argocd

```