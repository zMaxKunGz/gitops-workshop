# GitOps Workshop

Learn how to streamline software delivery with GitOps using Argo CD. Manage infrastructure and deploy applications with Git as the single source of truth. Automate updates and maintain consistency across all environments.

## Prerequisites

### Tools
- **kubectl** (Kubernetes command-line tool).

### Kubernetes Cluster
- **Option 1**: Set up a local Kubernetes cluster (e.g., Minikube, kind, Docker Desktop).
- **Option 2**: Access a remote Kubernetes cluster..

### GitHub Access
- A GitHub account is required.

## Hands-on Lab

### 1. Set Up a Kubernetes Cluster
Choose one of the following tools to set up your cluster:
- Docker Desktop
- Rancher Desktop
- Orbstack
- kind
- k3s
- Minikube

Once set up, verify your cluster is running:

```sh
kubectl get nodes
```

### 2. Install Argo CD

1. Create a namespace for Argo CD.

    ```sh
    kubectl create namespace argocd
    ```

2. Install Argo CD.

    ```sh
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
    ```

3. Access the Argo CD Web UI.

    Forward the port and open the UI in your browser:

    ```sh
    kubectl port-forward svc/argocd-server -n argocd 8080:80
    ```

   Visit: [http://localhost:8080](http://localhost:8080).

4. Login to Argo CD.

    Retrieve the admin password:

    ```sh
    kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
    ```

### 3. Create an App in Argo CD

1. Fork the public [workshop repository](https://github.com/ODDS-TEAM/gitops-workshop).

2. Create a new app in the Argo CD Web UI.

    - Use the forked repository as the source.
    - Configure the app with:
        - Enabled namespace creation
        - Enabled self-healing

### 4. Update the App

1. Update the app version by editing the `deployment.yaml` file in your repo:

    ```diff
    -   image: atbb/odds-conference:2024
    +   image: atbb/odds-conference:2025
    ```

2. Commit and push the changes.

3. Sync the app in Argo CD to deploy the update.

### 5. Rollback the App

1. Use the Argo CD **HISTORY AND ROLLBACK** feature to revert to an earlier version.

    - This will temporarily disable auto-sync to prevent self-healing.

2. Rollback with Git.

    Revert the last commit in your repository:

    ```sh
    git revert HEAD
    ```

### 6. Deploy App with Helm

1. Create a new app in the Argo CD Web UI.

2. Use the following Helm chart as the source: [https://github.com/stakater/application](https://github.com/stakater/application).

3. Update the Helm values:

    - Set `deployment.image.repository` to:

        ```sh
        atbb/odds-conference:2024
        ```

### 7. Clean Up

1. Delete the app in Argo CD.

2. Remove Argo CD resources

    ```sh
    kubectl delete -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
    ```

3. Delete the namespace

    ```sh
    kubectl delete namespace odds-conference
    ```
