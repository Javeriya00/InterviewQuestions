# Knowing Helm: Deploying Applications with Multi-Environment Support

## 🚀 Getting Started with Helm for Kubernetes Deployments

Helm simplifies Kubernetes application deployments by using **charts**, which package Kubernetes manifests and configuration files together. This guide covers Helm basics and how to manage **multiple environments (Dev, UAT, Prod)** efficiently.

---

## 📌 Setting Up Helm on Windows
```sh
winget install Helm.Helm  # Install Helm on Windows
```

## 📌 Creating & Exploring a Helm Chart
```sh
helm create mychart  # Generates the boilerplate Helm structure
cd mychart
```

This command creates a Helm chart with the following structure:
```
mychart/
│── charts/                 # Dependencies
│── templates/              # Kubernetes YAML templates
│── values.yaml             # Default values
│── Chart.yaml              # Chart metadata
│── .helmignore             # Files to ignore
```

## 📌 Verifying Minikube Cluster
```sh
helm list  # Check if any Helm releases exist
kubectl get nodes  # Verify Minikube is running
```

---

## 📌 Customizing the Helm Chart
Modify these files to fit your application needs:
✅ `templates/deployment.yaml` – Defines how your app runs  
✅ `templates/service.yaml` – Exposes the app within the cluster  

---

## 📌 Deploying with Helm
```sh
helm install my-firstchart ./ --wait
kubectl get pods  # Check if the deployment was successful
kubectl get svc  # Verify the service is running
```

## 📌 Accessing the Application
```sh
kubectl port-forward service/spring-boot-app-service 8088:80
```

---

# 🎯 Helm for Multi-Environment Deployments (Dev, UAT, Prod)
Helm makes it easy to manage multiple environments by **separating configuration from templates**.

### 🔹 The Challenge
Different environments require different configurations:
- **Dev:** Lower resource limits, fewer replicas  
- **UAT:** Staging-like setup for testing  
- **Prod:** High availability, autoscaling, stricter security  

### 🔹 The Solution – Separate `values.yaml` Files
Instead of modifying `values.yaml` manually, create separate values files:
```
mychart/
│── values.yaml        # Default values
│── values-dev.yaml    # Dev environment
│── values-uat.yaml    # UAT environment
│── values-prod.yaml   # Prod environment
```

### 🔹 What Changes Between Environments?
#### ✅ Replica Count (Scalability)
```yaml
# values-dev.yaml
replicaCount: 1

# values-prod.yaml
replicaCount: 5
```

#### ✅ Resource Limits
```yaml
# values-dev.yaml
resources:
  limits:
    cpu: "500m"
    memory: "512Mi"

# values-prod.yaml
resources:
  limits:
    cpu: "2"
    memory: "4Gi"
```

#### ✅ Database URLs
```yaml
# values-dev.yaml
database:
  url: "dev-db.mycompany.com"

# values-prod.yaml
database:
  url: "prod-db.mycompany.com"
```

#### ✅ Ingress Hostnames
```yaml
# values-dev.yaml
ingress:
  enabled: true
  host: "dev.myapp.com"

# values-prod.yaml
ingress:
  enabled: true
  host: "prod.myapp.com"
```

---

## 📌 Deploying to Different Environments
Using **Helm values files**, deploy the same application with different settings:
```sh
# Deploy to Dev
helm upgrade --install mychart ./ -f values-dev.yaml --namespace dev

# Deploy to UAT
helm upgrade --install mychart ./ -f values-uat.yaml --namespace uat

# Deploy to Prod
helm upgrade --install mychart ./ -f values-prod.yaml --namespace prod
```

Instead of manually tweaking YAML files, **Helm dynamically replaces the values based on the target environment.** 🚀

---

## 📌 Managing Helm Releases
```sh
helm list --all               # List all Helm releases
helm list --all-namespaces    # Check releases across all namespaces
helm list -o yaml             # Output in YAML format
helm list -o json             # Output in JSON format
```

---

## 🚀 Key Takeaways
✅ **Helm simplifies Kubernetes deployments** by separating configuration and templates.  
✅ **Using different `values.yaml` files**, we can deploy the same chart across multiple environments.  
✅ **Helm works well with CI/CD pipelines**, making automated deployments much easier.  

---

## 📌 Conclusion
Helm is a game-changer when it comes to managing **multi-environment Kubernetes deployments**. By leveraging `values.yaml`, we can streamline our deployments and maintain a consistent structure across Dev, UAT, and Prod environments.

🔹 Have you used **Helm for multi-environment deployments**? Feel free to share your experience!

#DevOps #Kubernetes #Helm #CloudNative #CI/CD

