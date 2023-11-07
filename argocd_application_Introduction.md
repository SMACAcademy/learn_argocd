# ArgoCD Apps management

## Creating Apps Via UI
```
Repo : https://github.com/SMACAcademy/argocd-example-apps.git
path : guestbook
```

## Creating Apps Via CLI
```
kubectl config set-context --current --namespace=argocd

argocd app create guestbookcli --repo https://github.com/SMACAcademy/argocd-example-apps.git --path guestbook --dest-server https://kubernetes.default.svc --dest-namespace default

```