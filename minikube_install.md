# Install ArgoCD

## install ArgoCD in minikube
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml


## Download ArgoCD CLI
https://github.com/argoproj/argo-cd/releases/latest

## Test argocd cli installation
argocd

## access ArgoCD UI
kubectl get svc -n argocd
kubectl port-forward svc/argocd-server 8080:443 -n argocd

localhost:8080

## login with admin user and below token either using kubectl or argocd:
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode && echo

argocd admin initial-password -n argocd


