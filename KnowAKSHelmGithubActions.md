# GitHub Actions + AKS + Helm Charts Learning Guide

## Overview
This guide provides a structured approach to learning **GitHub Actions, Azure Kubernetes Service (AKS), and Helm Charts** to help you build CI/CD pipelines and deploy applications efficiently.

---

## 1️⃣ GitHub Actions (CI/CD for Kubernetes)
### **What to Focus On?**
✅ Understanding GitHub Actions workflows (`.github/workflows/*.yaml`)
✅ Setting up **OIDC for Azure authentication**
✅ Deploying Docker images to **Azure Container Registry (ACR)**
✅ Running Kubernetes deployments using GitHub Actions
✅ Automating AKS deployment using GitHub Actions

### **Resources**
📖 [GitHub Actions Docs](https://docs.github.com/en/actions)

### **Example Workflow for AKS Deployment**
```yaml
name: Deploy to AKS
on: push
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Login to Azure
        uses: azure/login@v1
        with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
      
      - name: Deploy to AKS
        run: |
          az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
          kubectl apply -f k8s/deployment.yaml
```

---

## 2️⃣ Azure Kubernetes Service (AKS)
### **What to Focus On?**
✅ Understanding Kubernetes architecture (Pods, Services, Deployments, Ingress)
✅ Setting up AKS & configuring `kubectl`
✅ Deploying applications to AKS
✅ Managing AKS authentication & security (RBAC, Service Principals)
✅ Monitoring AKS workloads

### **Resources**
📖 [Microsoft AKS Docs](https://learn.microsoft.com/en-us/azure/aks/)

### **Basic AKS Setup Commands**
```sh
# Get AKS credentials
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster

# Verify connection
kubectl get nodes
```

---

## 3️⃣ Helm Charts (Kubernetes Package Management)
### **What to Focus On?**
✅ Understanding **Helm charts** (`values.yaml`, `Chart.yaml`)
✅ Deploying applications using Helm
✅ Helm upgrade, rollback, and release management
✅ Customizing Helm charts for AKS
✅ Storing Helm charts in Azure Container Registry (ACR)

### **Resources**
📖 [Helm Official Docs](https://helm.sh/docs/)

### **Basic Helm Commands**
```sh
# Install an application using Helm
helm install myapp ./helm-chart --namespace mynamespace

# Upgrade an application
helm upgrade myapp ./helm-chart --namespace mynamespace

# Rollback a release
helm rollback myapp 1 --namespace mynamespace
```

---

## 🎯 Next Steps
1. **Set up a GitHub Actions pipeline** to deploy a containerized application to AKS.
2. **Use Helm to package and deploy applications** instead of `kubectl apply`.
3. **Automate deployments** by integrating GitHub Actions with Helm.
4. **Monitor AKS workloads** using Azure Monitor and Logs.

🚀 Happy Learning! Let me know if you need any clarifications!

