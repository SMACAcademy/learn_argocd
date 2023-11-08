# Restarting ArgoCD server

kubectl rollout restart -n argocd deployment argocd-server
kubectl rollout restart -n argocd statefulset argocd-application-controller

