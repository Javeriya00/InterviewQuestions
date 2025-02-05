# AWS Interview Questions

## 1. **What AWS services have you worked with?**
I have experience working with the following AWS services:
- **EC2** (Elastic Compute Cloud) – Virtual servers for running applications.
- **EBS** (Elastic Block Store) – Persistent block storage for EC2 instances.
- **S3** (Simple Storage Service) – Scalable object storage for data backups and media.
- **IAM** (Identity and Access Management) – User access control and resource permissions.
- **EKS** (Elastic Kubernetes Service) – Managed Kubernetes clusters.
- **RDS** (Relational Database Service) – Managed relational database service (e.g., MySQL, PostgreSQL).
- **Lambda** – Serverless compute for running functions without managing servers.
- **CloudWatch** – Monitoring and logging for AWS resources.
- **CloudFormation** – Infrastructure as Code (IaC) service for provisioning AWS resources.
- **ALB** (Application Load Balancer) – Distributes incoming application traffic across multiple targets.
- **NLB** (Network Load Balancer) – Distributes traffic at the network layer for high-performance applications.

## 2. **Explain the difference between EBS and S3.**
- **EBS (Elastic Block Store)**:
  - Block-level storage designed for EC2 instances.
  - Typically used for applications that require high-performance storage (e.g., databases).
  - Persistent storage, meaning data remains intact even after EC2 instance termination.
  - Example usage: Storing OS, databases, or transactional data.

- **S3 (Simple Storage Service)**:
  - Object-level storage for large-scale data storage (e.g., backups, media files, logs).
  - Scalable, highly available, and durable.
  - Data is stored in objects within buckets (containers).
  - Example usage: Storing static website files, backups, media files, and logs.

## 3. **How do you provision an EC2 instance using Terraform?**
To provision an EC2 instance using Terraform, create the following configuration:

1. **Define provider:**
```hcl
provider "aws" {
  region = "us-west-2"
}
```

2. **Create an EC2 instance resource:**
```hcl
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0" # Replace with the appropriate AMI ID
  instance_type = "t2.micro"

  tags = {
    Name = "ExampleInstance"
  }
}
```

3. **Initialize Terraform and apply the configuration:**
```bash
terraform init
terraform apply
```

Terraform will create the EC2 instance with the defined parameters.

## 4. **What is an EKS cluster, and how does it differ from ECS?**
- **EKS (Elastic Kubernetes Service)**:
  - Managed Kubernetes service that simplifies the setup, operation, and scaling of Kubernetes clusters.
  - Provides full control over Kubernetes resources and can integrate with Kubernetes-native tools like Helm, kubectl, and other Kubernetes services.
  - Ideal for containerized applications that need orchestration across multiple instances.

- **ECS (Elastic Container Service)**:
  - AWS's own container orchestration service.
  - Supports Docker containers, but does not provide Kubernetes features (unless integrated with Amazon EKS).
  - Suitable for simpler containerized applications, offering seamless integration with other AWS services like Fargate (serverless compute for containers).

## 5. **How do you configure auto-scaling for an EC2 instance?**
To configure auto-scaling for EC2 instances, use **Auto Scaling Groups** (ASG):

1. **Create a launch configuration:**
```hcl
resource "aws_launch_configuration" "example" {
  name = "example-launch-config"
  image_id = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}
```

2. **Create an Auto Scaling Group:**
```hcl
resource "aws_autoscaling_group" "example" {
  desired_capacity = 2
  max_size         = 5
  min_size         = 1
  vpc_zone_identifier = ["subnet-12345"]

  launch_configuration = aws_launch_configuration.example.id
}
```

3. **Configure scaling policies:**
```hcl
resource "aws_autoscaling_policy" "scale_up" {
  scaling_adjustment = 1
  adjustment_type   = "ChangeInCapacity"
  cooldown          = 300
  metric_aggregation_type = "Average"
  auto_scaling_group_name = aws_autoscaling_group.example.name
}
```

This will allow EC2 instances to scale based on demand.

## 6. **How do you secure an S3 bucket?**
To secure an S3 bucket, follow these steps:
1. **Disable public access**:
   - Use the S3 block public access setting to ensure no public access to your bucket.

2. **Use IAM policies to control access**:
   - Restrict access by using IAM roles/policies to grant permissions only to authorized users.
   
Example IAM policy to restrict access to a specific user:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

3. **Enable encryption**:
   - Use SSE (Server-Side Encryption) to encrypt data at rest.

## 7. **What are IAM roles, and how do you assign them?**
- **IAM Roles**: These are entities that define a set of permissions that can be assumed by AWS services or other IAM users/groups.
- **Assigning IAM Roles**:
  - You can assign IAM roles to EC2 instances, Lambda functions, or other AWS resources to allow them to access AWS services securely.

Example of an IAM role assignment for an EC2 instance:
```hcl
resource "aws_iam_role" "example" {
  name = "example-role"
  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Principal = {
          Service = "ec2.amazonaws.com"
        }
        Action = "sts:AssumeRole"
      }
    ]
  })
}

resource "aws_iam_instance_profile" "example" {
  name = "example-instance-profile"
  role = aws_iam_role.example.name
}

resource "aws_instance" "example" {
  ami = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  iam_instance_profile = aws_iam_instance_profile.example.name
}
```

## 8. **What is an ALB, and how does it differ from an NLB?**
- **ALB (Application Load Balancer)**:
  - Operates at the **application layer** (Layer 7 of the OSI model).
  - Routes traffic based on HTTP/HTTPS requests, supports path-based routing, and allows SSL termination.
  - Ideal for web applications requiring advanced routing features.

- **NLB (Network Load Balancer)**:
  - Operates at the **network layer** (Layer 4 of the OSI model).
  - Handles TCP and UDP traffic with very low latency.
  - Suitable for high-performance applications that require fast network-level routing.

## 9. **How do you set up a CI/CD pipeline to deploy infrastructure using Terraform on AWS?**
To set up a CI/CD pipeline using Terraform on AWS:

1. **Create a CodeCommit repository for Terraform configuration files.**
2. **Set up AWS CodePipeline:**
   - Source stage: Fetches the Terraform configuration from CodeCommit.
   - Build stage: Uses AWS CodeBuild to execute `terraform plan` and `terraform apply` commands.

Example of a CodeBuild configuration for Terraform:
```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      java: corretto8
    commands:
      - echo Installing Terraform
      - curl -LO https://releases.hashicorp.com/terraform/1.0.11/terraform_1.0.11_linux_amd64.zip
      - unzip terraform_1.0.11_linux_amd64.zip
      - mv terraform /usr/local/bin/
  build:
    commands:
      - terraform init
      - terraform plan
      - terraform apply -auto-approve
```

3. **Trigger the pipeline automatically** when changes are pushed to the repository.

## 10. **Explain the role of AWS CodeBuild, CodePipeline, and how they compare with GitLab CI/CD.**
- **AWS CodeBuild**: A fully managed build service that compiles source code, runs tests, and produces deployable artifacts.
- **AWS CodePipeline**: A fully managed CI/CD service that automates the software release process, integrating with CodeBuild, CodeDeploy, and other AWS services.
- **GitLab CI/CD**: Offers a similar pipeline capability but with more flexibility, including integration with various external services. GitLab allows hosting your CI/CD pipeline on your own infrastructure or GitLab-hosted runners.

Key differences:
- **AWS CodePipeline** is tightly integrated with AWS services, while **GitLab CI/CD** offers more flexibility for multi-cloud or hybrid environments.

---
