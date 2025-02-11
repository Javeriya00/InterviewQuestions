# How to Get AZURE_CLIENT_ID and AZURE_TENANT_ID for GitHub Actions

## Overview
To deploy an application to **Azure Kubernetes Service (AKS)** using **GitHub Actions**, you need to authenticate with Azure. This requires an **Azure Service Principal**, which provides credentials such as:

- **AZURE_CLIENT_ID** → The **Application (Client) ID** of your Service Principal.
- **AZURE_TENANT_ID** → The **Azure Active Directory (AAD) Tenant ID**.
- **AZURE_SUBSCRIPTION_ID** → The **Azure Subscription ID** (optional but recommended).

This guide walks you through retrieving these values.

---

## 🚀 Step 1: Log in to Azure
First, open a terminal and log in to your Azure account:

```sh
az login
```

If you’re using a **service principal** with a secret (headless authentication), use:

```sh
az login --service-principal -u <APP_ID> -p <PASSWORD> --tenant <TENANT_ID>
```

---

## 🚀 Step 2: Create a Service Principal (SP)
If you don’t already have one, create a new **Service Principal** with Contributor role for AKS:

```sh
az ad sp create-for-rbac --name "my-aks-github-sp" --role Contributor --scopes /subscriptions/<SUBSCRIPTION_ID>
```

### Example Output:
```json
{
  "appId": "11111111-2222-3333-4444-555555555555",
  "displayName": "my-aks-github-sp",
  "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenant": "66666666-7777-8888-9999-000000000000"
}
```

---

## 🚀 Step 3: Retrieve the Required Values
From the output above:

| GitHub Secret Name    | Value from Azure Output |
|----------------------|------------------|
| **AZURE_CLIENT_ID** | `appId` → "11111111-2222-3333-4444-555555555555" |
| **AZURE_TENANT_ID** | `tenant` → "66666666-7777-8888-9999-000000000000" |
| **AZURE_SUBSCRIPTION_ID** | Run the following command to retrieve: |

```sh
az account show --query id -o tsv
```

---

## 🚀 Step 4: Store in GitHub Secrets
1. Go to your **GitHub Repository**
2. Navigate to **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret** and add the following:

| Secret Name | Value |
|-----------------------|-----------------|
| `AZURE_CLIENT_ID` | App ID (Client ID) |
| `AZURE_TENANT_ID` | Tenant ID |
| `AZURE_SUBSCRIPTION_ID` | Subscription ID |

---

## 🎯 Summary
- **AZURE_CLIENT_ID** → Azure Service Principal's App ID
- **AZURE_TENANT_ID** → Azure Directory Tenant ID
- **AZURE_SUBSCRIPTION_ID** → Your Azure Subscription ID
- **Store them securely in GitHub Secrets**

With these credentials stored, your GitHub Actions workflow can securely authenticate to Azure! 🚀

