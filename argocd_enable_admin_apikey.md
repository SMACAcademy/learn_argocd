
# API Key capability for the 'admin' accounts

Enable the `apiKey` capability for the `admin` account. Here's how you can do this:


- **Edit the ArgoCD ConfigMap**:

First, you need to edit the `argocd-cm` ConfigMap to grant the `admin` account the ability to create API keys.

```sh

kubectl edit configmap argocd-cm -n argocd

```
This will open the ConfigMap in your default text editor.
- **Modify the ConfigMap**:

In the editor, look for the `data` section, then add or update the `accounts.admin` field to include `apiKey`. It should look something like this:

```yaml

data:
  accounts.admin: apiKey, login

```
If the `accounts.admin` field is not present, you will need to add it with the `apiKey` capability.
- **Save and Exit**:

After making the changes, save the file and exit the text editor. The changes will be applied to the ArgoCD configuration.
- **Restart the ArgoCD API Server**:

It may be necessary to restart the ArgoCD API server for the changes to take effect.

```sh

kubectl rollout restart deployment argocd-server -n argocd

```
- **Generate the API Key**:

Once the API server is back up and running, you should be able to generate an API key for the `admin` account without encountering the previous error.

```sh
argocd account generate-token --account admin

```