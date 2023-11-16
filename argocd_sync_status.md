Below are the commands, sample files, and YAML configurations to demonstrate the sync status in ArgoCD. We will simulate the scenarios to understand different sync statuses.

**Part 1: Initial Setup**

Create a new application in ArgoCD using the command-line interface with the following configuration:

**Sample Application YAML (demo-app.yaml):**

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: demo-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: 'https://github.com/your-org/demo-app-repo.git'
    targetRevision: HEAD
    path: k8s-manifests
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: demo
  syncPolicy:
    automated:
      prune: true
      selfHeal: true

```
**Command to create the application from the YAML file:**

```bash
kubectl apply -f demo-app.yaml

```
**Part 2: Exploring Sync Statuses**

**Scenario 1: Synced Status**


- After applying the YAML, ArgoCD will sync the application. You can watch the status by accessing the ArgoCD dashboard or using the CLI:

```bash
argocd app get demo-app

```
**Scenario 2: OutOfSync Status**


- Update the image tag in the deployment manifest in the Git repository. Then, use the following command to see the `OutOfSync` status:

```bash
argocd app get demo-app

```
**Scenario 3: Unknown Status**


- Apply a network policy that blocks ArgoCD's access to the Kubernetes API server:

**Sample Network Policy (network-policy.yaml):**

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-argocd
  namespace: demo
spec:
  podSelector:
    matchLabels:
      app.kubernetes.io/name: argocd-server
  policyTypes:
  - Egress
  egress: []

```
**Command to apply the network policy:**

```bash
kubectl apply -f network-policy.yaml

```

- After applying this policy, the `argocd app get` command should show an `Unknown` status for the `demo-app`.

**Scenario 4: Sync Failed Status**


- Introduce an error into the deployment manifest in the Git repository. For instance, use an incorrect API version for a Deployment resource.

**Sample Deployment Manifest with Error (deployment.yaml):**

```yaml
apiVersion: apps/v1wrong
kind: Deployment
metadata:
  name: bad-deployment
  labels:
    app: bad-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bad-app
  template:
    metadata:
      labels:
        app: bad-app
    spec:
      containers:
      - name: nginx
        image: nginx:latest

```

- Commit and push this change to the Git repository. ArgoCD will attempt to sync and then show a `Sync Failed` status:

```bash
argocd app get demo-app

```
**Part 3: Commands for Removing Finalizers**

If an application is stuck in deletion due to finalizers, you can manually remove the finalizers with the following command:

```bash
kubectl patch application demo-app -n argocd -p '{"metadata":{"finalizers":[]}}' --type=merge

```

