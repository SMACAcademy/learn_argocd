### 1. Introduction to Kustomize
Start with a theoretical introduction to Kustomize:


- What is Kustomize?
- How does Kustomize work within the Kubernetes ecosystem?
- Advantages of using Kustomize with ArgoCD.

### 2. Pre-requisites
Ensure that students have:


- Access to a Kubernetes cluster.
- Installed ArgoCD in their Kubernetes cluster.
- Basic understanding of Kubernetes resource files.
- Installed Kustomize and kubectl on their local machine.

### 3. Sample Application Structure
Provide a sample directory structure for a simple application, such as a "Hello World" web service. This should include:


- **Deployment.yaml**: Define a basic deployment with a single replica.
- **Service.yaml**: Define a service to expose the deployment.
- **Kustomization.yaml**: Define resources, configurations, patches, and other customizations.

### 4. Kustomization Examples
Provide examples of Kustomization files for different environments (development, staging, production), which may include:


- **base/**: Base Kustomization file with common resource configurations.
- **overlays/dev/**: Development-specific configurations such as replica count, resource limits, etc.
- **overlays/staging/**: Staging-specific configurations.
- **overlays/production/**: Production-specific configurations.


### Base Configuration
In the `base` directory, you typically have your Kubernetes manifests that are common across all environments. Here's an example of what the contents of these files might look like.

**base/deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-world
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: web
        image: "nginx:stable"
        ports:
        - containerPort: 80

```
**base/service.yaml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-world
spec:
  type: LoadBalancer
  selector:
    app: hello-world
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

```
**base/kustomization.yaml**

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - deployment.yaml
  - service.yaml

```
### Overlays for Different Environments
For each environment, you create an `overlays` directory, which contains environment-specific configurations. Here's an example of what the `overlays` for `development`, `staging`, and `production` might include.

**overlays/development/kustomization.yaml**

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
bases:
  - ../../base
patchesStrategicMerge:
  - deployment_patch.yaml
namePrefix: dev-

```
**overlays/development/deployment_patch.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world
spec:
  replicas: 1

```
**overlays/staging/kustomization.yaml**

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
bases:
  - ../../base
patchesStrategicMerge:
  - deployment_patch.yaml
namePrefix: staging-

```
**overlays/staging/deployment_patch.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world
spec:
  replicas: 2

```
**overlays/production/kustomization.yaml**

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
bases:
  - ../../base
patchesStrategicMerge:
  - deployment_patch.yaml
namePrefix: prod-

```
**overlays/production/deployment_patch.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world
spec:
  replicas: 3

```
In the `development`, `staging`, and `production` overlays, we have different `deployment_patch.yaml` files that override the number of replicas specified in the base `deployment.yaml`. This demonstrates how you can customize your application for different environments using Kustomize. Each `kustomization.yaml` in the overlays directory references the base configuration and specifies a patch file and a name prefix to differentiate resources by environment.



### 5. ArgoCD Application Manifest
Create a sample ArgoCD application manifest (`app.yaml`) that defines how ArgoCD should deploy the application using Kustomize:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: sample-app
spec:
  project: default
  source:
    repoURL: 'https://github.com/your-username/sample-app.git'
    targetRevision: HEAD
    path: overlays/production
    kustomize:
      namePrefix: prod-
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: production
  syncPolicy:
    automated:
      selfHeal: true
      prune: true

```
### 6. Command Line Usage
Explain how to use kubectl with Kustomize to apply the configuration locally:

```bash
kubectl apply -k overlays/production/

```
### 7. ArgoCD Command Line
Teach students how to use the ArgoCD CLI to create an application from a manifest file:

```bash
argocd app create -f app.yaml
```
And how to sync the application:

```bash
argocd app sync sample-app
```


