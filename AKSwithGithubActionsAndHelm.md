# Deploy a Spring Boot App to AKS Using GitHub Actions & Helm

This guide walks through deploying a **Spring Boot** application to **Azure Kubernetes Service (AKS)** using **GitHub Actions and Helm**. It includes:
- Setting up an AKS cluster
- Creating and using a Helm chart
- Automating deployments with GitHub Actions
- Adding an Ingress Controller for custom domain access
- Implementing Helm versioning and rollbacks

---

## Prerequisites
Ensure you have the following installed:
- [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [Helm](https://helm.sh/docs/intro/install/)
- A **GitHub Repository** for CI/CD
- A **Dockerized Spring Boot Application** pushed to **Docker Hub**

---

## 1️⃣ Set Up an AKS Cluster
Create an AKS cluster:
```sh
az group create --name myResourceGroup --location eastus
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 2 --enable-addons monitoring --generate-ssh-keys
```
Connect to the cluster:
```sh
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
kubectl get nodes
```

---

## 2️⃣ Create a Helm Chart for Your Spring Boot App
Create a Helm chart:
```sh
helm create springboot-chart
cd springboot-chart
```
Edit `values.yaml`:
```yaml
image:
  repository: docker.io/your-dockerhub-username/your-springboot-app
  tag: latest
  pullPolicy: IfNotPresent

service:
  type: LoadBalancer
  port: 80
  targetPort: 8080
```
Edit `deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: springboot-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: springboot-app
  template:
    metadata:
      labels:
        app: springboot-app
    spec:
      containers:
        - name: springboot-app
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - containerPort: 8080
```

Package and push the Helm chart:
```sh
helm package .
helm repo index .
```

---

## 3️⃣ Set Up GitHub Actions for CI/CD
Create `.github/workflows/deploy.yml`:
```yaml
name: Deploy to AKS using Helm

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v3

      - name: Azure Login
        uses: azure/login@v1
        with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}

      - name: Set up Kubernetes
        run: |
          az aks get-credentials --resource-group myResourceGroup --name myAKSCluster

      - name: Install Helm
        run: |
          curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

      - name: Deploy Helm Chart
        run: |
          helm upgrade --install springboot-app ./springboot-chart --namespace default
```

### Set Up GitHub Secrets:
Go to **GitHub Repository → Settings → Secrets and Variables → Actions** and add:
- `AZURE_CLIENT_ID`
- `AZURE_TENANT_ID`
- `AZURE_SUBSCRIPTION_ID`

Push changes to trigger GitHub Actions:
```sh
git add .
git commit -m "Deploy Spring Boot App to AKS using Helm"
git push origin main
```

---

## 4️⃣ Add Ingress Controller to Expose the App via a Custom Domain
Install **NGINX Ingress Controller**:
```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```
Create an **Ingress resource** (`ingress.yaml`):
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: springboot-ingress
spec:
  rules:
  - host: myapp.example.com  # Replace with your domain
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: springboot-app
            port:
              number: 80
```
Apply the Ingress resource:
```sh
kubectl apply -f ingress.yaml
```
Get the Ingress Controller’s external IP:
```sh
kubectl get svc -n ingress-nginx
```
Update your **DNS settings** to point your domain to this external IP.

---

## 5️⃣ Implement Helm Versioning and Rollbacks
### Upgrade Helm Chart with Versioning:
```sh
helm upgrade --install springboot-app ./springboot-chart --namespace default --version 1.0.0
```
### Rollback to a Previous Version:
```sh
helm rollback springboot-app 1
```
Check rollout history:
```sh
helm history springboot-app
```

---

## 🔥 Verification & Debugging
Check pod status:
```sh
kubectl get pods
```
Check logs:
```sh
kubectl logs -l app=springboot-app
```
Test the service:
```sh
kubectl get svc
curl http://<EXTERNAL-IP>
```

---

## 🚀 Next Steps
- Automate **database migrations** using Helm hooks
- Integrate **Prometheus & Grafana** for monitoring
- Improve **RBAC security policies**

---

### 🎯 Congratulations! 🎯
Your **Spring Boot app** is now running on **AKS** with **GitHub Actions & Helm**! 🎉

Let me know if you need modifications! 🚀

