
- **Create a Secret in Kubernetes**: Label the secret so that Argo CD can recognize it as containing repository credentials.

For HTTPS credentials:

```sh
kubectl create secret generic \
  --namespace argocd \
  --from-literal=username= \
  --from-literal=password= \
  --from-literal=url= \
  --type=Opaque \
  --dry-run=client -o yaml | \
  kubectl label -f - argocd.argoproj.io/secret-type=repository --local --dry-run=client -o yaml | \
  kubectl apply -f -

```
For an SSH private key:

```sh
kubectl create secret generic \
  --namespace argocd \
  --from-file=sshPrivateKey= \
  --from-literal=url= \
  --type=Opaque \
  --dry-run=client -o yaml | \
  kubectl label -f - argocd.argoproj.io/secret-type=repository --local --dry-run=client -o yaml | \
  kubectl apply -f -

```
- **Associate the Secret with the Repository in Argo CD**: You don't need to manually edit the Argo CD config to link the secret. By labeling the secret correctly, Argo CD will pick it up automatically.

- **Verify Repository Connection**: To confirm that Argo CD can access the repository using the credentials from the secret, you can use:

```sh

argocd repo list

```

This command will list all configured repositories along with their connection status.

Please replace `<secret-name>`, `<your-username>`, `<your-password>`, `<repository-url>`, and `<path-to-private-key>` with your actual repository details. The `--dry-run=client -o yaml` part of the command is used to output the resource definition to standard output, which is then piped to `kubectl apply` to create the resource in your cluster. The `--local` flag is used to make changes locally before applying them.

