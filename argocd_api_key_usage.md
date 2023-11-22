# Sample usecase on using API Key

**Scenario:**
Automate the deployment of a new version of the application every time there's a push to the `main` branch into the Git repository. You've set up a CI/CD pipeline in a tool like Jenkins, GitHub Actions, or GitLab CI, and you need this pipeline to trigger ArgoCD to synchronize the changes from the Git repository to your Kubernetes cluster.

## Steps:


### 1. Generate an API Key in ArgoCD:
   First, you need to generate an API key. As of the current capabilities of ArgoCD, this would typically be done using the CLI by a user with sufficient permissions:
    
   ```sh
   argocd account generate-token --account <your-account-name>
   ```
   Replace `<your-account-name>` with the name of the account for which you are generating the token.

### 2. Store the API Key Securely:
   Store this API key in a secure location, like a secrets manager, or as a secret in your CI/CD pipeline's configuration. Make sure it is not exposed in logs or stored in version control.

### 3. Use the API Key in Your CI/CD Pipeline:
   When configuring your CI/CD pipeline, use the API key to authenticate API calls to ArgoCD. Here's an example of how you might use `curl` to trigger a synchronization using the API key:
   ```sh
   
   curl -X POST https://<argocd-server>/api/v1/applications/<application-name>/sync \
   -H "Authorization: Bearer <your-api-key>" \
   -H "Content-Type: application/json" \
   --data '{"revision": "HEAD"}' --insecure

  ```
   Replace <argocd-server> with your ArgoCD server's address, <application-name> with the name of your ArgoCD application, and <your-api-key> with the API key you've generated.

### 4. Automate Token Rotation:
   For security reasons, it is good practice to rotate your API tokens periodically. You can automate this process by scripting the generation of a new token and updating your CI/CD pipeline's secrets accordingly.

### 5. Monitor and Audit Usage:
   Regularly monitor the use of API keys and audit them to ensure they are not being misused. You can set up monitoring on your ArgoCD instance to watch for API calls and potentially suspicious activities.

By using an API key in this manner, you can securely automate interactions with ArgoCD without needing to manually trigger each deployment. This streamlines your workflow and ensures your application deployments are as up-to-date as your source code repository.

