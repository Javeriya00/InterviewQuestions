# GitHub Actions for AKS Deployment with Helm and Caching

## Overview
This repository demonstrates how to set up **GitHub Actions** to deploy a **Spring Boot application** to **Azure Kubernetes Service (AKS)** using **Helm**. It also covers best practices like caching dependencies to speed up workflows.

## Workflow Features
- **CI/CD for Java Spring Boot App**
- **Docker Image Build & Push** to Docker Hub
- **Helm Deployment to AKS**
- **Caching for Maven, Docker, and NPM**

## Prerequisites
- An **Azure account** with an **AKS cluster**
- **Docker Hub account** for pushing images
- **GitHub repository** with your project
- **Secrets configured in GitHub**:
  - `AZURE_CLIENT_ID` (Azure Service Principal ID)
  - `AZURE_TENANT_ID` (Azure Tenant ID)
  - `AZURE_SUBSCRIPTION_ID` (Azure Subscription ID)
  - `DOCKERHUB_USERNAME` (Docker Hub username)
  - `DOCKERHUB_TOKEN` (Docker Hub token)

## GitHub Actions Workflow
Create a `.github/workflows/deploy-aks.yml` file in your repository with the following content:

```yaml
name: Build and Deploy to AKS

on: push

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Cache Maven Dependencies
        uses: actions/cache@v3
        with:
          path: ~/.m2
          key: maven-${{ hashFiles('**/pom.xml') }}
          restore-keys: maven-

      - name: Build with Maven
        run: mvn clean package

      - name: Cache Docker Layers
        run: |
          docker build --cache-from=type=gha --cache-to=type=gha,mode=max -t my-app .

      - name: Login to Docker Hub
        run: echo "${{ secrets.DOCKERHUB_TOKEN }}" | docker login -u "${{ secrets.DOCKERHUB_USERNAME }}" --password-stdin

      - name: Push Docker Image
        run: |
          docker tag my-app:latest docker.io/${{ secrets.DOCKERHUB_USERNAME }}/my-app:latest
          docker push docker.io/${{ secrets.DOCKERHUB_USERNAME }}/my-app:latest

      - name: Login to Azure
        uses: azure/login@v1
        with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}

      - name: Set AKS Context
        uses: azure/aks-set-context@v3
        with:
          resource-group: my-resource-group
          cluster-name: my-aks-cluster

      - name: Deploy with Helm
        run: |
          helm upgrade --install my-app ./helm-chart \
            --namespace default \
            --set image.repository=docker.io/${{ secrets.DOCKERHUB_USERNAME }}/my-app \
            --set image.tag=latest
```

## Caching Strategy
Caching is used to optimize the workflow and reduce build times.

### **Maven Dependencies**
```yaml
- name: Cache Maven Dependencies
  uses: actions/cache@v3
  with:
    path: ~/.m2
    key: maven-${{ hashFiles('**/pom.xml') }}
    restore-keys: maven-
```

### **Docker Layer Caching**
```yaml
- name: Cache Docker Layers
  run: |
    docker build --cache-from=type=gha --cache-to=type=gha,mode=max -t my-app .
```

## Choosing GitHub Actions
When selecting actions, follow these guidelines:
1. **Use Official GitHub Actions** (e.g., `actions/checkout`, `azure/login`)
2. **Check GitHub Marketplace** ([GitHub Actions Marketplace](https://github.com/marketplace?type=actions))
3. **Look for Verified Creators** (e.g., GitHub, Microsoft, Docker)
4. **Check Stars & Forks** (more stars = widely used)

## Summary
- **GitHub Actions automates deployment to AKS**
- **Uses Helm for Kubernetes deployment**
- **Caching speeds up builds**
- **Secrets are stored securely in GitHub**

---
This setup ensures fast, reliable, and automated deployments. 🚀

