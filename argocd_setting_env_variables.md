# Ways of setting environmental variables

## setting using kubectl command

kubectl set env -n argocd deployment/argocd-repo-server ARGOCD_RECONCILIATION_TIMEOUT=5s

## Set Kubernetes environment variables using the Linux CLI

export ARGOCD_RECONCILIATION_TIMEOUT=5s

## Set argocd application parameter

argocd app set myguestbook -p ARGOCD_RECONCILIATION_TIMEOUT=5s