# AWS DevOps Interview Questions and Answers  

If you find this useful, give it a ⭐! Contributions are most welcome. 🚀  

---  

## What AWS services have you worked with?  
I have worked with a variety of AWS services across different areas of cloud infrastructure, including compute, networking, storage, and security. Below are some of the key AWS services I've worked with:  

### Compute  
- **EC2 (Elastic Compute Cloud):** Used for provisioning and managing virtual machines for various workloads.  
- **ECS (Elastic Container Service):** Deployed Docker containers on ECS for managing microservices in production.  
- **EKS (Elastic Kubernetes Service):** Managed Kubernetes clusters on AWS for deploying containerized applications using Helm and Terraform.  
- **Lambda:** Implemented serverless architecture for event-driven workflows, handling tasks like notifications and data processing.  

### Storage  
- **S3 (Simple Storage Service):** Used for storing backups, logs, and static files. Integrated with CloudFront for content delivery.  
- **EBS (Elastic Block Store):** Mounted as persistent storage for EC2 instances.  
- **EFS (Elastic File System):** Used for shared file storage between multiple EC2 instances.  

### Networking  
- **VPC (Virtual Private Cloud):** Designed and managed network configurations, including subnets, security groups, and route tables.  
- **ELB (Elastic Load Balancer):** Configured and managed load balancing for scaling web applications.  
- **Route 53:** Managed DNS for mapping domain names to IP addresses, integrated with CloudFront for low-latency content delivery.  

### Security  
- **IAM (Identity and Access Management):** Managed user access and permissions, implemented least-privilege policies, and used IAM roles for secure service-to-service communication.  
- **Secrets Manager:** Stored and managed secrets (e.g., API keys, database credentials) securely for applications.  
- **GuardDuty:** Enabled threat detection and monitoring for suspicious activities across AWS resources.  
- **KMS (Key Management Service):** Used for encrypting data at rest, including S3, EBS volumes, and RDS instances.  

---  

## How do you configure auto-scaling for an EC2 instance?  
Auto Scaling in AWS is configured using an **Auto Scaling Group (ASG)** and a **Launch Template** or **Launch Configuration**.  

### Steps to configure auto-scaling:  
1. **Create a Launch Template:**  
   ```hcl  
   resource "aws_launch_template" "example" {  
     name_prefix   = "my-template"  
     image_id      = "ami-0c55b159cbfafe1f0"  
     instance_type = "t2.micro"  
   }  
   ```  
2. **Create an Auto Scaling Group (ASG):**  
   ```hcl  
   resource "aws_autoscaling_group" "example" {  
     desired_capacity = 2  
     max_size         = 5  
     min_size         = 1  
     launch_template {  
       id      = aws_launch_template.example.id  
       version = "$Latest"  
     }  
   }  
   ```  
3. **Define Scaling Policies:**  
   - **Target Tracking:** Scale based on CPU, memory, or custom metrics.  
   - **Step Scaling:** Define scaling thresholds.  
   - **Scheduled Scaling:** Scale at specific times.  
4. **Attach an Elastic Load Balancer (Optional):**  
   ```hcl  
   resource "aws_lb" "example" {  
     name               = "my-load-balancer"  
     internal           = false  
     load_balancer_type = "application"  
     security_groups    = [aws_security_group.example.id]  
     subnets            = aws_subnet.public[*].id  
   }  
   ```  
5. **Monitor ASG using CloudWatch and fine-tune policies accordingly.**  

---  

## How do you secure an S3 bucket?  
To secure an S3 bucket, implement the following best practices:  
- ✅ **Block Public Access:** Use S3 settings to block all public access.  
- ✅ **IAM Policies:** Define least privilege IAM policies to control access.  
- ✅ **Bucket Policies:** Restrict access to specific AWS accounts or IP ranges.  
- ✅ **Enable Server-Side Encryption:** Use `SSE-S3`, `SSE-KMS`, or `SSE-C` to encrypt data at rest.  
- ✅ **Enable MFA Delete:** Require multi-factor authentication for object deletion.  
- ✅ **Use Access Logs:** Enable S3 logging to track access.  
- ✅ **Enable AWS WAF:** Protect against malicious access attempts.  
- ✅ **Set Lifecycle Policies:** Automate data retention and deletion.  

---  

## What is an ALB, and how does it differ from an NLB?  

| Feature | ALB (Application Load Balancer) | NLB (Network Load Balancer) |  
|---------|---------------------------------|-----------------------------|  
| **Protocol** | HTTP/HTTPS | TCP, UDP, TLS |  
| **Layer** | Layer 7 (Application Layer) | Layer 4 (Transport Layer) |  
| **Routing** | Path-based, Host-based | Direct IP-based |  
| **Performance** | Slightly higher latency | Ultra-low latency |  
| **Sticky Sessions** | Supported | Not supported (without extra config) |  
| **SSL Termination** | Supported | Requires TLS listeners |  
| **Use Case** | Web applications, Microservices | Low-latency, real-time applications |  

---  

## How do you set up a CI/CD pipeline to deploy infrastructure using Terraform on AWS?  

### Steps to configure a Terraform CI/CD pipeline:  
1. **Setup Git Repository:** Store Terraform code in GitHub/GitLab.  
2. **Create a GitLab CI/CD Pipeline (`.gitlab-ci.yml`):**  
   ```yaml  
   stages:  
     - plan  
     - apply  
   plan:  
     script:  
       - terraform init  
       - terraform plan  
   apply:  
     script:  
       - terraform apply -auto-approve  
   ```  
3. **Use Terraform Cloud/Backend:** Configure **AWS S3** as a backend for state management.  
4. **Configure AWS Credentials:** Use IAM roles or environment variables.  
5. **Use GitLab Runners or AWS CodeBuild:** Execute Terraform jobs on a managed CI/CD runner.  

---  

This document is a structured guide for AWS DevOps interview preparation, covering auto-scaling, security, IAM, load balancing, Terraform CI/CD, and AWS DevOps tools. Happy learning! 🚀

