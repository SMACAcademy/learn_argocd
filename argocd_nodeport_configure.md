# ArgoCD server nodeport configure

```
kubectl get all -n argocd


kubectl patch svc argocd-server -n argocd -p \
  '{"spec": {"type": "NodePort", "ports": [{"name": "http", "nodePort": 30080, "port": 80, "protocol": "TCP", "targetPort": 8080}, {"name": "https", "nodePort": 30443, "port": 443, "protocol": "TCP", "targetPort": 8080}]}}'

kubectl describe svc argocd-server -n argocd

minikube service --all -n argocd

--Note  the url for argocd server


argocd admin initial-password -n argocd



```


