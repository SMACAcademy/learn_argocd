## Introduction

- Explanation of ArgoCD and its benefits for Kubernetes deployment.
- Introduction to Helm and how it simplifies package management in Kubernetes.

## Prerequisites

- Kubernetes cluster (e.g., Minikube, EKS, GKE, AKS).
- Installed ArgoCD in the Kubernetes cluster.
- Helm installed locally or in a CI/CD environment.

## Part 1: Setting up Helm in Your Local Environment

- Step-by-step instructions for installing Helm.
- Creating a new Helm chart or using an existing one.

## Part 2: Understanding Helm Charts

- Structure of a Helm chart.
- Explanation of Chart.yaml, values.yaml, templates, and other components.

## Part 3: Creating a Simple Helm Chart

- Command: `helm create my-application`
- Sample files:
   - Chart.yaml
   - values.yaml
   - Deployment.yaml
   - Service.yaml

### Directory Structure
```markdown
my-webapp/
  Chart.yaml
  values.yaml
  charts/
  templates/
    deployment.yaml
    service.yaml
    ingress.yaml

```
#### Chart.yaml
This is the metadata file for the Helm chart.

```yaml
apiVersion: v2
name: my-webapp
description: A Helm chart for Kubernetes

# Version of the application
version: 0.1.0

```
#### values.yaml
Values to inject into your Helm chart templates.

```yaml
replicaCount: 1

image:
  repository: nginx
  pullPolicy: IfNotPresent
  tag: "stable"

service:
  type: LoadBalancer
  port: 80

```
#### templates/deployment.yaml
Defines the Kubernetes deployment resource.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "my-webapp.fullname" . }}
  labels:
    {{- include "my-webapp.labels" . | nindent 4 }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      {{- include "my-webapp.selectorLabels" . | nindent 6 }}
  template:
    metadata:
      labels:
        {{- include "my-webapp.selectorLabels" . | nindent 8 }}
    spec:
      containers:
        - name: nginx
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - containerPort: 80
              name: http
          resources:
            {{- toYaml .Values.resources | nindent 12 }}

```
#### templates/service.yaml
Defines the Kubernetes service resource.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ include "my-webapp.fullname" . }}
  labels:
    {{- include "my-webapp.labels" . | nindent 4 }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: http
      protocol: TCP
      name: http
  selector:
    {{- include "my-webapp.selectorLabels" . | nindent 4 }}

```
#### templates/ingress.yaml
Optional: If you want to define an Ingress resource for your application, you can define it here.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: {{ include "my-webapp.fullname" . }}
  annotations:
    kubernetes.io/ingress.class: nginx
spec:
  rules:
    - host: "my-webapp.local"
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: {{ include "my-webapp.fullname" . }}
                port:
                  number: 80

```
After creating these files, you would package the chart using `helm package my-webapp`.

### ArgoCD Application Manifest
Now, let's define an ArgoCD application manifest to deploy this Helm chart:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-webapp
  namespace: argocd
spec:
  project: default
  source:
    repoURL: 'https://charts.bitnami.com/bitnami'
    targetRevision: HEAD
    chart: my-webapp
    helm:
      parameters:
        - name: service.type
          value: LoadBalancer
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: default
  syncPolicy:
    automated:
      selfHeal: true
      prune: true

```
Replace `https://charts.bitnami.com/bitnami` with the URL of your chart repository.

You can now apply this manifest to your ArgoCD instance with `kubectl apply -f application.yaml`, and ArgoCD will manage the lifecycle of your web application in Kubernetes using Helm.


### Part 4: Packaging the Helm Chart

- Command: `helm package my-application`

### Part 5: Deploying Helm Chart with ArgoCD

- Creating a repository in ArgoCD for Helm charts.
- Command to create an ArgoCD application:
   - `argocd app create my-helm-app --repo [REPO_URL] --path [CHART_PATH] --dest-server [K8S_CLUSTER_URL] --dest-namespace [NAMESPACE]`

### Part 6: Sample ArgoCD Application Manifest for Helm

- Sample `Application.yaml` file that uses Helm:```yaml
Copy code
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-helm-app
spec:
  project: default
  source:
    repoURL: 'https://[HELM_CHART_REPO_URL]'
    targetRevision: HEAD
    path: charts/my-application
    helm:
      valueFiles:
        - values.yaml
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: default

```
- Explanation of each field and how to customize the `Application.yaml`.

#### Part 7: Syncing the Application

- Command: `argocd app sync my-helm-app`

#### Part 8: Updating the Application

- How to update Helm chart values.
- Syncing changes through ArgoCD.