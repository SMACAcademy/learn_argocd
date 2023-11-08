# Modify the Application Reconciliation Timeout in Argo CD
```
kubectl describe configmaps argocd-cm -n argocd

kubectl edit configmaps argocd-cm -n argocd

## Change reconciliation to the duration required.

apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
  namespace: argocd
  labels:
    app.kubernetes.io/name: argocd-cm
    app.kubernetes.io/part-of: argocd
data:
  timeout.reconciliation: 60s
. . .


kubectl -n argocd rollout restart deploy argocd-repo-server


printenv | grep -i timeout

ARGOCD_RECONCILIATION_TIMEOUT=0

export ARGOCD_RECONCILIATION_TIMEOUT=15s


Minikube to be restarted. If argocd running in minikube


```

kubectl set env -n argocd deployment/argocd-repo-server ARGOCD_RECONCILIATION_TIMEOUT=5s

kubectl describe -n argocd deployment/argocd-repo-server