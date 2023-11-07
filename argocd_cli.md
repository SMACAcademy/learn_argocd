# ArgoCD CLI

## Login using ArgoCD CLI admin

```

argocd login SERVER
argocd login http://kubernetes.default.svc

argocd login 192.168.49.2:30443


argocd admin initial-password -n argocd

argocd cluster list

argocd proj list

argocd app list

argocd repo list

argocd logout 192.168.49.2:30443

argocd repo list

argocd login 192.168.49.2:30443 --insecure --username admin --password XYUJoHIpByiJk2nT

argocd proj list

```

## Create project

```
argocd proj create myproj

argocd proj list


argocd account get-user-info

argocd account update-password


# Create a directory app
argocd app create guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path guestbook --dest-namespace default --dest-server https://kubernetes.default.svc --directory-recurse

# Create a Jsonnet app
argocd app create jsonnet-guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path jsonnet-guestbook --dest-namespace default --dest-server https://kubernetes.default.svc --jsonnet-ext-str replicas=2

# Create a Helm app
argocd app create helm-guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path helm-guestbook --dest-namespace default --dest-server https://kubernetes.default.svc --helm-set replicaCount=2

# Create a Helm app from a Helm repo
argocd app create nginx-ingress --repo https://kubernetes-charts.storage.googleapis.com --helm-chart nginx-ingress --revision 1.24.3 --dest-namespace default --dest-server https://kubernetes.default.svc

# Create a Kustomize app
argocd app create kustomize-guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path kustomize-guestbook --dest-namespace default --dest-server https://kubernetes.default.svc --kustomize-image gcr.io/heptio-images/ks-guestbook-demo:0.1

# Create a app using a custom tool:
argocd app create ksane --repo https://github.com/argoproj/argocd-example-apps.git --path plugins/kasane --dest-namespace default --dest-server https://kubernetes.default.svc --config-management-plugin kasane


argocd app list

argocd app get guestbook


```