# High Availability Setup for ArgoCD

Setting up high availability for ArgoCD involves deploying it in a Kubernetes cluster with multiple replicas of its components. This ensures that if one instance fails, others can take over.

## Prerequisites

- A Kubernetes cluster with at least 3 worker nodes.
- `kubectl` CLI installed and configured.
- Helm CLI installed.

## Installation Steps

1. **Add ArgoCD Helm Repository**:

   ```
   helm repo add argo https://argoproj.github.io/argo-helm
   helm repo update
   ```

2. Create a values.yaml File for HA Configuration

   Create a values.yaml file to override the default configuration to enable HA:

   ```yaml
   # values.yaml
   controller:
     replicaCount: 2

   server:
     replicaCount: 2
     service:
       type: LoadBalancer

   redis:
     replicaCount: 2
     sentinel:
       enabled: true

   dex:
     replicaCount: 2

   ``` 
   Additional configurations argocd-cm.yaml

   ```yaml
   # argocd-cm.yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: argocd-cm
     namespace: argocd
   data:
     # Add your custom configurations here
     some-key: some-value

   ```

   You'll likely need a secrets file to store sensitive information like SSH keys or access tokens for repository access:

   ```yaml
   
   # argocd-secret.yaml
   apiVersion: v1
   kind: Secret
   metadata:
     name: argocd-secret
     namespace: argocd
   type: Opaque
   data:
     # Secrets must be base64 encoded
     # e.g., echo -n 'mysecret' | base64
     sshPrivateKey: <base64-encoded-private-key>
     githubToken: <base64-encoded-github-token>

   ```


3. Deploy ArgoCD using the HA Configuration

   Use Helm to install ArgoCD with your HA values.yaml:
   
   ```
   helm install argocd argo/argo-cd -f values.yaml --namespace argocd --create-namespace

   ``` 
   Then, if you have custom configurations, apply them as follows:

   ```
   kubectl apply -f argocd-cm.yaml
   kubectl apply -f argocd-secret.yaml
   
   ```


4. Verify the Deployment
   
   Ensure that all the pods are running and that the services have endpoints:
   ```
   kubectl get pods,services -n argocd

   or

   kubectl get all -n argocd


   ```

5. Access the ArgoCD UI
   
   The ArgoCD server service should be exposed as a LoadBalancer (if your cluster supports it), allowing you to access the ArgoCD UI and API:

   ```
   kubectl get service argocd-server -n argocd
   ```

6. Sample Files
