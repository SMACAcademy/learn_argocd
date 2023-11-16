To create a sample application in ArgoCD that demonstrates Rolling Updates, follow these steps:

### Prerequisites

- A Kubernetes cluster with Istio installed.
- ArgoCD installed on the cluster.
- Two versions of your application container images available (e.g., `v1.0` and `v1.1`).
- 
### 2. Set Up a Sample Application
Create a sample application deployment manifest (`sample-app-deployment.yaml`) with a rolling update strategy defined in it. Here's a simple example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-app
  labels:
    app: sample-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: sample-app
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
  template:
    metadata:
      labels:
        app: sample-app
    spec:
      containers:
      - name: nginx-container
        image: nginx:1.19.0
        ports:
        - containerPort: 80

```
### 3. Commit to a Git Repository
ArgoCD follows the GitOps principles, so you need to commit this YAML file to a Git repository. This repository will be used by ArgoCD to monitor and apply changes.

### 4. Connect Your Repository to ArgoCD
Use the following command to connect your Git repository to ArgoCD:

```bash
argocd repo add <your-git-repo-url>

```
### 5. Create an Application in ArgoCD
Now you will create an ArgoCD application that points to your repository. You can do this either via the ArgoCD CLI or the UI.

Using the CLI, execute:

```bash
argocd app create sample-app \
--repo <your-git-repo-url> \
--path . \
--dest-server https://kubernetes.default.svc \
--dest-namespace default

```
Make sure to replace `<your-git-repo-url>` with the actual URL of your Git repository.

### 6. Deploy the Application
To deploy the application through ArgoCD, use:

```bash
argocd app sync sample-app

```
### 7. Perform a Rolling Update
To perform a rolling update, you can change the image in your `sample-app-deployment.yaml` to a new version:

```yaml
...
      containers:
      - name: nginx-container
        image: nginx:1.19.1 # Change this to a new version
...

```
Commit and push this change to your Git repository. ArgoCD will detect the change and perform a rolling update based on the strategy defined in the deployment manifest.

### 8. Verify the Update
Use the following command to watch the rolling update:

```bash
kubectl rollout status deployment/sample-app

```
This command will show you the update's progress.

By following these steps, you've created a sample application in ArgoCD that can demonstrate a rolling update strategy. Remember to adjust the commands according to your specific Git repository, Kubernetes cluster, and application details.
