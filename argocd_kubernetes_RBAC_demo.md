Role-Based Access Control (RBAC) in Kubernetes is a method for regulating access to the Kubernetes API, where you define rules about who can do what within a Kubernetes cluster. Here's a simple example that includes a role definition, a role binding, and a service account.

### 1. Service Account
Create a file named `service-account.yaml` with the following content:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: example-service-account
  namespace: default

```
This YAML defines a Service Account named `example-service-account` in the `default` namespace.

### 2. Role
Create a file named `role.yaml` with the following content:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]

```
This `Role` named `pod-reader` allows a user to perform "get", "watch", and "list" actions on pods within the `default` namespace.

### 3. Role Binding
Create a file named `role-binding.yaml` with the following content:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: ServiceAccount
  name: example-service-account
  namespace: default
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io

```
This `RoleBinding` named `read-pods` grants the permissions defined in the `pod-reader` role to the `example-service-account` service account.

### Usage
To apply these configurations to your Kubernetes cluster, you would run the following `kubectl` commands in your terminal:

```sh
kubectl apply -f service-account.yaml
kubectl apply -f role.yaml
kubectl apply -f role-binding.yaml

```
Each command applies the respective configuration to your cluster. After applying these, the service account `example-service-account` will have the permissions defined by the `pod-reader` role within the `default` namespace.


### Verification

To confirm that RBAC is working as expected, you can perform tests to ensure that the service account can access the resources permitted by the role and that it's denied access to anything not explicitly allowed.

### Positive Test Case: Accessing Allowed Resources
For the positive test case, you want to verify that the `example-service-account` can indeed get, list, and watch pods in the `default` namespace as allowed by the `pod-reader` role.


- Try to get the list of pods:

```sh
kubectl get pods

kubectl get pods --as=system:serviceaccount:default:example-service-account

```
This command should succeed, showing you the list of pods in the `default` namespace.

### Negative Test Case: Accessing Forbidden Resources
For the negative test case, you want to verify that the `example-service-account` is denied access to resources not allowed by the `pod-reader` role.


- Try to create a new pod:

```sh
kubectl run nginx --image=nginx

kubectl run nginx --image=nginx --as=system:serviceaccount:default:example-service-account

```
This should fail because the `pod-reader` role does not allow creating pods.


- Try to access resources in a different namespace or resources not included in the role:

```sh
kubectl get pods --namespace=kube-system

kubectl get pods --namespace=kube-system --as=system:serviceaccount:default:example-service-account

```
or

```sh
kubectl get secrets

```
Both commands should fail since the role does not grant access to resources in other namespaces or to secrets in any namespace.

### Verifying the Results
When a command is denied due to RBAC, Kubernetes will return an error message stating that access was denied. For example:

```vbnet
Error from server (Forbidden): pods is forbidden: User "system:serviceaccount:default:example-service-account" cannot list resource "pods" in API group "" in the namespace "kube-system"

```
This message confirms that the RBAC is in effect, as it's preventing the service account from performing actions that are not allowed by its role.

### Automated Testing with `kubectl auth can-i`
For automated testing, you can use the `kubectl auth can-i` command to check whether a certain action is allowed or not:


- Check if the service account can list pods:

```sh
kubectl auth can-i list pods --as=system:serviceaccount:default:example-service-account

```
The output should be `yes`.


- Check if the service account can delete nodes, which should not be permitted:

```sh
kubectl auth can-i delete nodes --as=system:serviceaccount:default:example-service-account

```
The output should be `no`.

Remember to replace `--as=system:serviceaccount:default:example-service-account` with the appropriate service account if you are using a different one or namespace.

By performing these tests, you can confirm that the RBAC permissions are applied as expected.

### Reset kubeconfig context back to use minikube user

```
kubectl config set-context --current --user=minikube

kubectl get pods --namespace=kube-system

kubectl get pods --namespace=kube-system --as=system:serviceaccount:default:example-service-account

```