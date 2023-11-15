### Prerequisites

- An accessible Kubernetes cluster
- ArgoCD installed on the cluster
- Two versions of your application container images available (e.g., `v1.0` and `v1.1`)

### Sample Application Setup

- **Create Two Deployments**:
   - One deployment for the 'Blue' version of your application (e.g., `v1.0`)
   - Another deployment for the 'Green' version of your application (e.g., `v1.1`)
- **Create a Service**:
   - A single service that acts as the stable endpoint for your application users
- **Configure Routing**:
   - Initially, configure the service to route traffic to the 'Blue' deployment

### ArgoCD Application Manifests
Here's what the Kubernetes manifests could look like:

```yaml
# blue-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-app-blue
  labels:
    app: sample-app
    version: v1.0
spec:
  replicas: 3
  selector:
    matchLabels:
      app: sample-app
      version: v1.0
  template:
    metadata:
      labels:
        app: sample-app
        version: v1.0
    spec:
      containers:
      - name: sample-app
        image: your-registry/sample-app:v1.0
        ports:
        - containerPort: 80

# green-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-app-green
  labels:
    app: sample-app
    version: v1.1
spec:
  replicas: 3
  selector:
    matchLabels:
      app: sample-app
      version: v1.1
  template:
    metadata:
      labels:
        app: sample-app
        version: v1.1
    spec:
      containers:
      - name: sample-app
        image: your-registry/sample-app:v1.1
        ports:
        - containerPort: 80

# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: sample-app-service
spec:
  selector:
    app: sample-app
    version: v1.0  # Initially pointing to Blue
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

```
### Blue-Green Deployment Process
**To Perform Blue-Green Deployment:**


- **Initial State**: The service routes traffic to the 'Blue' deployment (`v1.0`).
- **Deploy 'Green'**: Deploy the 'Green' version (`v1.1`) alongside 'Blue', without routing user traffic to it.
- **Test 'Green'**: Perform all necessary testing on the 'Green' deployment to ensure it is ready for production.
- **Switch Traffic**: Once you're confident the 'Green' deployment is ready, update the service to route traffic to 'Green' (`v1.1`) by changing the service's selector.

```yaml
# service.yaml updated selector
spec:
  selector:
    app: sample-app
    version: v1.1  # Now pointing to Green

```

- **Monitor 'Green'**: Carefully monitor the 'Green' deployment to ensure it operates as expected.
- **Finalize or Rollback**: If an issue arises, switch the service selector back to 'Blue'. If everything is stable, the 'Blue' deployment can be decommissioned.

### ArgoCD Synchronization

- Once the manifests are prepared, commit them to your Git repository.
- Configure an ArgoCD Application to track this repository.
- Use ArgoCD to synchronize and manage these deployments.

```shell<button class="flex ml-auto gizmo:ml-0 gap-2 items-center"><svg width="16" height="16" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M9 5H7C5.89543 5 5 5.89543 5 7V19C5 20.1046 5.89543 21 7 21H17C18.1046 21 19 20.1046 19 19V7C19 5.89543 18.1046 5 17 5H15" stroke="currentColor" stroke-width="2"></path><path d="M9 6C9 4.34315 10.3431 3 12 3V3C13.6569 3 15 4.34315 15 6V6C15 6.55228 14.5523 7 14 7H10C9.44772 7 9 6.55228 9 6V6Z" stroke="currentColor" stroke-width="2" stroke-linejoin="round"></path></svg>Copy code</button>
argocd app create sample-app \
  --repo your-git-repository-url \
  --path path-to-manifests \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default

```
### Explanation during Deployment
As you step through each phase of the Blue-Green deployment, explain the purpose and actions:


- The rationale behind having two separate deployments
- The role of the service as the traffic router
- The importance of testing the 'Green' environment before switching traffic
- The ability to quickly rollback in case of any issues by simply pointing the service back to 'Blue'

This method ensures zero downtime and provides a safety net for quick reversion if something goes wrong. The key to Blue-Green deployment is having an immediate switch-over mechanism and ensuring both environments are production-ready.

By demonstrating this process in ArgoCD, you provide a visual and practical example of how Blue-Green deployments can be managed



------------------

**You:**

For the above explanation, provide the commands for the steps mentioned at "To Perform Blue-Green Deployment:"

------------------

**ChatGPT:**

To perform a Blue-Green deployment as outlined in the explanation, you would use `kubectl` commands to apply your manifests and update the service selector. Here are the commands for each step:


- **Deploy 'Blue' Version**:
Initially, you have the 'Blue' version deployed and servicing traffic.

```
kubectl apply -f blue-deployment.yaml
kubectl apply -f service.yaml

```
- **Deploy 'Green' Version**:
Deploy the 'Green' version alongside the 'Blue', but don't route traffic to it yet.

```
kubectl apply -f green-deployment.yaml

```
- **Test 'Green' Version**:
You'd perform your testing on the 'Green' deployment using its internal service or port-forwarding.

```
kubectl port-forward svc/sample-app-green 8080:80

```
Open another terminal and perform your testing against `localhost:8080`.
- **Switch Traffic to 'Green'**:
Once testing is complete and you're ready to switch traffic to 'Green', update the service selector.

You can do this by editing the service directly:

```
kubectl patch svc sample-app-service -p '{"spec": {"selector": {"version": "v1.1"}}}'

```
Or apply a new service manifest where the selector is already set to 'Green':

```
kubectl apply -f service-green.yaml  # Assuming this is your updated service manifest pointing to Green

```
- **Monitor 'Green' Version**:
After switching, monitor the 'Green' deployment to ensure it operates as expected. For demonstration purposes, you might simply watch the pod statuses:

```
kubectl get pods -l version=v1.1 -w

```
- **Finalize or Rollback**:
If everything is stable, you can decommission the 'Blue' deployment:

```
kubectl delete deployment sample-app-blue

```
If you need to rollback, switch the service selector back to 'Blue':

```
kubectl patch svc sample-app-service -p '{"spec": {"selector": {"version": "v1.0"}}}'

```
And if necessary, scale the 'Green' deployment down:

```
kubectl scale deployment sample-app-green --replicas=0

```

These commands assume you're using `kubectl` and have the necessary permissions to perform operations on the cluster. In a real-world scenario, these steps might be automated in a CI/CD pipeline, and there would be additional considerations for database migrations, persistent connections, and other stateful aspects of your application.


