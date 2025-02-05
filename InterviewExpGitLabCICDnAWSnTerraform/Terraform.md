# Terraform Interview Questions and Answers

If you find this useful, give it a ⭐! Contributions are most welcome. 🚀

---

### **What is Terraform, and why is it used?**
Terraform is an Infrastructure as Code (IaC) tool developed by HashiCorp that allows users to define and provision infrastructure using a declarative configuration language (HCL - HashiCorp Configuration Language). It enables automation, consistency, and repeatability in deploying cloud resources across various providers like AWS, Azure, and Google Cloud.

### **What are Terraform state files? How do you manage them?**
Terraform state files (`terraform.tfstate`) keep track of the real-world resources Terraform manages. They store metadata and mappings of resources to configurations.

#### **Managing Terraform state:**
- **Local State:** Stored on the local machine, useful for single-user projects.
- **Remote State:** Stored in remote locations (e.g., AWS S3, Terraform Cloud) to enable collaboration.
- **State Locking:** Prevents simultaneous updates using DynamoDB (for AWS S3 backend).
- **State Encryption:** Protects sensitive data in state files using KMS or encryption features of storage solutions.

### **Difference between `terraform apply` and `terraform plan`?**
| Command | Description |
|---------|-------------|
| `terraform plan` | Shows the changes Terraform will make without applying them. Useful for reviewing before deployment. |
| `terraform apply` | Applies the planned changes to provision infrastructure. Requires user confirmation unless `-auto-approve` is used. |

### **How do you handle secrets in Terraform?**
- **Environment Variables:** Store secrets in environment variables (`TF_VAR_secret` for variable input).
- **AWS Secrets Manager or HashiCorp Vault:** Fetch secrets dynamically instead of hardcoding them.
- **Terraform Variables:** Use `variable.tf` with sensitive attributes (`sensitive = true`).
- **Remote State Encryption:** Encrypt Terraform state to protect secrets.

### **What are Terraform modules?**
Terraform modules are reusable sets of Terraform configurations that help organize and manage infrastructure efficiently. They provide:
- **Code reusability**
- **Easier maintenance**
- **Standardization**
- **Simplified organization**

Example of a module:
```hcl
module "network" {
  source  = "./modules/network"
  vpc_id  = "vpc-12345"
}
```

---

## **Intermediate Questions**

### **How do you manage Terraform state in a team?**
- **Remote State Storage:** Use AWS S3, Terraform Cloud, or Consul for shared state storage.
- **State Locking:** Use DynamoDB for AWS S3 to prevent race conditions.
- **Access Control:** Implement IAM roles and policies to restrict access.
- **Versioning:** Enable S3 versioning to track changes.
- **Backup & Restore:** Maintain automated snapshots for disaster recovery.

### **What are Terraform workspaces, and when do you use them?**
Terraform workspaces allow managing multiple environments (e.g., dev, staging, prod) using a single Terraform configuration.
- **Command to create a workspace:** `terraform workspace new dev`
- **Command to switch workspace:** `terraform workspace select dev`
- **Use case:** Manage different configurations for multiple environments without separate Terraform codebases.

### **Explain Terraform backend configurations.**
Backends define where Terraform stores its state files. Examples:
```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "state/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-lock"
  }
}
```
- **Local Backend:** Stores state locally (not recommended for teams).
- **Remote Backend:** Stores state remotely (S3, Terraform Cloud, Azure Blob, etc.).
- **Enhanced Security:** Supports encryption, locking, and access control.

### **How do you debug Terraform errors?**
- **Enable Debug Mode:** `TF_LOG=DEBUG terraform apply`
- **Plan Output Analysis:** Check the `terraform plan` output for inconsistencies.
- **Validate Configuration:** Run `terraform validate` to check for syntax errors.
- **Refresh State:** `terraform refresh` to sync Terraform state with real infrastructure.
- **Terraform Console:** Use `terraform console` to inspect variables and expressions.
- **Enable Trace Logging:** Use `TF_LOG=TRACE` for detailed debugging.

---

## **Advanced Questions**

### **Explain the difference between `terraform import` and `terraform taint`.**
| Feature | `terraform import` | `terraform taint` |
|---------|-------------------|------------------|
| Purpose | Imports existing resources into Terraform state. | Marks a resource for forced recreation in the next `apply`. |
| Example | `terraform import aws_instance.example i-123456` | `terraform taint aws_instance.example` |
| Effect | Adds resource to state without modifying infrastructure. | Forces resource to be destroyed and recreated. |

### **How do you implement Terraform for multi-cloud deployments?**
- **Use Multi-Provider Configuration:** Define different providers in Terraform.
- **Example: Deploy to AWS & Azure:**
```hcl
provider "aws" {
  region = "us-east-1"
}
provider "azurerm" {
  features {}
}
resource "aws_s3_bucket" "example" {
  bucket = "multi-cloud-bucket"
}
resource "azurerm_storage_account" "example" {
  name                     = "multicloudstorage"
  resource_group_name      = "my-rg"
  location                 = "East US"
}
```
- **Use Terraform Cloud/State Syncing:** Centralized management across clouds.
- **Implement Modular Design:** Organize cloud-specific modules.

### **How do you use Terraform with Kubernetes (EKS)?**
Terraform provisions Kubernetes clusters and deploys resources using the Kubernetes provider.
#### **Steps:**
1. **Provision EKS Cluster:**
```hcl
provider "aws" {
  region = "us-east-1"
}
module "eks" {
  source          = "terraform-aws-modules/eks/aws"
  cluster_name    = "my-eks-cluster"
  cluster_version = "1.23"
}
```
2. **Configure Kubernetes Provider:**
```hcl
provider "kubernetes" {
  host                   = module.eks.cluster_endpoint
  token                  = data.aws_eks_cluster_auth.cluster.token
  cluster_ca_certificate = base64decode(module.eks.cluster_certificate_authority_data)
}
```
3. **Deploy Workloads using Terraform:**
```hcl
resource "kubernetes_deployment" "nginx" {
  metadata {
    name = "nginx"
  }
  spec {
    replicas = 2
    selector {
      match_labels = {
        app = "nginx"
      }
    }
    template {
      metadata {
        labels = {
          app = "nginx"
        }
      }
      spec {
        container {
          image = "nginx:latest"
          name  = "nginx"
        }
      }
    }
  }
}
```

---
This document provides an extensive Terraform interview guide, covering fundamental, intermediate, and advanced topics. Happy learning! 🚀

