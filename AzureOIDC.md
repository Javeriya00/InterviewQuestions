# Using OIDC for Azure Authentication in GitHub Actions

## Overview
**OIDC (OpenID Connect)** allows GitHub Actions to authenticate directly with **Azure** without storing long-lived credentials like service principal secrets. This enhances **security** by enabling short-lived, **federated credentials** instead of secrets stored in GitHub.

---

## 🔹 Why Use OIDC for Azure Authentication?
✅ **More Secure** – No need to store Azure client secrets in GitHub.  
✅ **Short-Lived Tokens** – Reduces risk of credential leaks.  
✅ **Easier Management** – Uses federated identity without direct credential sharing.  
✅ **Native Support** – GitHub integrates with Azure through OpenID Connect.  

---

## 🔹 How to Use OIDC for GitHub Actions with Azure?
Follow these steps to set up OIDC-based authentication for GitHub Actions in Azure.

### **1️⃣ Create a Federated Identity Credential in Azure**
Run the following commands to configure OIDC authentication in **Azure**.

```sh
# Set variables
AZURE_SUBSCRIPTION_ID="your-subscription-id"
AZURE_RESOURCE_GROUP="your-resource-group"
AZURE_CLIENT_ID="your-app-client-id"
GITHUB_ORG="your-github-org" # OR your GitHub repo: username/repository

# Assign federated credentials to an Azure service principal
az ad app federated-credential create --id $AZURE_CLIENT_ID --parameters '{
  "name": "GitHubActionsOIDC",
  "issuer": "https://token.actions.githubusercontent.com",
  "subject": "repo:'"$GITHUB_ORG"':ref:refs/heads/main",
  "audiences": ["api://AzureADTokenExchange"]
}'
```

---

### **2️⃣ Configure GitHub Actions Workflow with OIDC Login**
In your **GitHub Actions workflow** (`.github/workflows/deploy.yml`), use **OIDC authentication** to log in to Azure:

```yaml
name: Deploy to Azure with OIDC
on: [push]

jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      id-token: write   # Required for OIDC authentication
      contents: read

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Azure Login via OIDC
        uses: azure/login@v1
        with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
          enable-AzPSSession: true  # Required for Azure CLI authentication

      - name: Verify AKS Access
        run: |
          az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
          kubectl get nodes
```

---

## 🔹 Key Points to Remember
1️⃣ **No Client Secret Required** → OIDC eliminates the need to store Azure credentials in GitHub Secrets.  
2️⃣ **Permissions Required** → Ensure GitHub **workflow permissions** include `id-token: write`.  
3️⃣ **Issuer & Subject Validation** → GitHub tokens are validated against the `issuer` and `subject` in Azure.  

---

## 🔹 Benefits of Using OIDC for AKS Deployments
🔹 **Improved Security** – Avoids long-lived secrets in GitHub.  
🔹 **Better Compliance** – Meets enterprise security best practices.  
🔹 **Scalability** – Ideal for large teams and multi-repo projects.  

---

## 🎯 Next Steps
- Implement OIDC authentication in your GitHub Actions workflows.
- Test authentication using `az login` and verify `kubectl` access.
- Apply OIDC across multiple repositories for better security.

🚀 Happy Deploying!
