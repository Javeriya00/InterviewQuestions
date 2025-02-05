
### **1. How do you automate a task using shell scripting in a CI/CD pipeline?**
  
In my CI/CD pipelines (e.g., GitLab CI/CD), I have used shell scripting to automate tasks such as:
- **Building Docker images**: I wrote shell scripts to build Docker images using the `docker build` command and pushed the images to a container registry.
- **Versioning updates**: I used shell scripting to automate the updating of version numbers or image tags in configuration files (e.g., `sed` commands for replacing version tags in Kubernetes YAML files).
- **Managing Kubernetes deployments**: I wrote scripts to apply Kubernetes manifests using `kubectl apply -f` and check the status of pods or deployments using `kubectl get pods`.
  
Example:
```bash
#!/bin/bash
# Build Docker image and push to Docker Hub
docker build -t myapp:$CI_COMMIT_SHA .
docker push myapp:$CI_COMMIT_SHA
```
This script would run within the GitLab pipeline as part of the CI stage to automate building and pushing the image.

---

### **2. Can you write a script to check if a Docker container is running and restart it if it’s not?**
  
Sure, here's a simple bash script to check if a specific Docker container is running and restart it if it's not:

```bash
#!/bin/bash
# Check if the container is running
container_name="my-container"
if ! docker ps | grep -q $container_name; then
  echo "Container $container_name is not running. Restarting..."
  docker start $container_name
else
  echo "Container $container_name is running."
fi
```

This script checks if the container is running using `docker ps`. If it’s not found, it will attempt to start it using `docker start`.

---

### **3. How do you use PowerShell for automating tasks in an Azure environment?**
  
In my project, I used **PowerShell** to automate tasks related to managing **Azure resources**. For example, to automate the deployment of Azure resources like VMs, networking, and storage, I used Azure PowerShell modules.

Example script to create a resource group in Azure:

```powershell
# Log in to Azure
Connect-AzAccount

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location "East US"
```

Additionally, PowerShell scripts were also used to automate the setup of **Azure Kubernetes Service (AKS)** clusters by provisioning resources and configuring kubectl.

---

### **4. Can you write a script to back up Kubernetes manifests to a remote repository (e.g., GitLab) automatically?**
  
Certainly! I can create a shell script that periodically backs up Kubernetes manifests (e.g., deployment YAML files) to a GitLab repository:

```bash
#!/bin/bash
# Define variables
repo_dir="/path/to/repo"
k8s_manifests_dir="/path/to/k8s/manifests"
commit_message="Backup Kubernetes Manifests"
git_url="https://gitlab.com/user/repo.git"

# Pull the latest changes
cd $repo_dir
git pull origin main

# Copy Kubernetes manifests to the repo folder
cp $k8s_manifests_dir/*.yaml $repo_dir/

# Commit and push the changes
git add .
git commit -m "$commit_message"
git push $git_url main
```

This script copies the Kubernetes manifests from the local directory, commits them to the GitLab repository, and pushes the changes to GitLab. It can be scheduled to run periodically using cron jobs.

---

### **5. How do you use Python to interact with AWS resources, such as S3 or EC2?**

In my project, I used **Python** and the **Boto3** library to interact with AWS resources. For example, to automate the process of backing up files to an **S3 bucket** or managing **EC2 instances**, I wrote Python scripts using **Boto3**.

Example of a Python script to upload files to S3:
```python
import boto3

# Initialize S3 client
s3 = boto3.client('s3')

# Upload file to S3
bucket_name = 'my-bucket'
file_name = 'my-file.txt'
s3.upload_file(file_name, bucket_name, file_name)
```

This script uploads `my-file.txt` to the specified S3 bucket using the `upload_file()` method provided by Boto3.

---

### **6. What are your best practices for writing maintainable scripts in a team environment?**

To ensure maintainability and collaboration in scripts, I follow these best practices:
- **Version Control**: Always store scripts in a Git repository (e.g., GitLab) to manage changes and version history.
- **Modularization**: Break down complex scripts into smaller, reusable functions to promote code reuse and reduce redundancy.
- **Logging**: Implement logging mechanisms (e.g., using `logger` in shell scripts or `logging` in Python) to provide detailed insights into script execution and errors.
- **Error Handling**: Always include error handling to gracefully exit the script and return meaningful error messages if something goes wrong.
- **Documentation**: Add comments and documentation to explain the purpose of the script, parameters, and important logic.

---

### **7. How do you use Bash to create and manage cron jobs for automation tasks?**

I use **cron jobs** to schedule tasks such as running backups, cleaning up logs, or checking the health of services. Here’s an example of creating a cron job in Bash to run a backup script every day at midnight:

1. **Create the Bash script** (e.g., `backup.sh`):

```bash
#!/bin/bash
# Backup script
tar -czf /backups/backup_$(date +%Y%m%d).tar.gz /important_data/
```

2. **Add to cron**:  
Use `crontab -e` to schedule the job:

```bash
# Cron entry to run backup at midnight every day
0 0 * * * /path/to/backup.sh
```

This cron job will automatically run the backup script every day at midnight.

---

### **8. Can you explain how you would write a script to monitor disk space on Linux and alert if it falls below a certain threshold?**

Here’s a shell script that checks disk space usage and sends an alert if it goes above 80%:

```bash
#!/bin/bash
# Check disk space usage
threshold=80
disk_usage=$(df / | grep / | awk '{ print $5 }' | sed 's/%//g')

if [ $disk_usage -gt $threshold ]; then
  echo "Disk space is above $threshold%. Sending alert!"
  # You can send an email or Slack notification here
  mail -s "Disk Space Alert" admin@example.com <<< "Disk space is at $disk_usage%, exceeding threshold."
fi
```

This script uses `df` to check disk space and triggers an alert if the usage exceeds the defined threshold.

---

### **9. How would you write a script to clean up unused Docker images and containers?**
 
I use a script to clean up unused Docker images, containers, and volumes to free up disk space:

```bash
#!/bin/bash
# Clean up unused Docker containers, images, and volumes

# Remove stopped containers
docker container prune -f

# Remove unused images
docker image prune -af

# Remove unused volumes
docker volume prune -f

# Remove unused networks
docker network prune -f
```

This script uses Docker's built-in `prune` command to clean up unused resources. It helps keep the environment lean and optimized.

---

### **10. How do you handle secrets securely in your scripts, especially in cloud environments?**
  
In my scripts, I make sure that secrets (API keys, passwords, etc.) are never hardcoded or exposed. I use the following approaches:
- **Environment Variables**: Store secrets in environment variables, which are securely retrieved by the script.
- **Cloud Secret Management**: In AWS, I use **AWS Secrets Manager** or **Parameter Store** to store and retrieve secrets. For Azure, I use **Azure Key Vault**.
- **Encrypted Files**: If secrets need to be stored in files, I ensure they are encrypted using tools like **GPG** or **OpenSSL**.
- **Vault Integration**: In some cases, I use **HashiCorp Vault** for dynamic secret management, integrating it with my scripts to fetch secrets when needed.

---

### **1. Bash Script to Backup Kubernetes Manifests**

**Purpose**: Automatically back up Kubernetes manifests from a local directory to a Git repository.

```bash
#!/bin/bash
# Backup Kubernetes manifests to GitLab repository

# Define variables
repo_dir="/path/to/repo"
manifests_dir="/path/to/k8s/manifests"
commit_message="Backup Kubernetes Manifests $(date +'%Y-%m-%d')"
git_url="https://gitlab.com/user/repo.git"

# Change to the repository directory
cd $repo_dir

# Pull the latest changes
git pull origin main

# Copy Kubernetes manifests to the repository folder
cp $manifests_dir/*.yaml $repo_dir/

# Stage, commit, and push changes
git add .
git commit -m "$commit_message"
git push $git_url main
```
**Explanation**:  
- This script will periodically back up Kubernetes manifests (YAML files) to a GitLab repository.
- You can set this script as a cron job to run regularly and ensure your manifests are backed up in Git.

---

### **2. Bash Script to Check Kubernetes Pod Status and Restart Failed Pods**

**Purpose**: Check if a Kubernetes pod is in a "CrashLoopBackOff" state and restart it.

```bash
#!/bin/bash
# Check if the pod is in a crash loop and restart it

namespace="default"
pod_name="my-app-pod"
pod_status=$(kubectl get pod $pod_name -n $namespace -o=jsonpath='{.status.phase}')

if [ "$pod_status" == "CrashLoopBackOff" ]; then
  echo "Pod $pod_name is in CrashLoopBackOff. Restarting..."
  kubectl delete pod $pod_name -n $namespace
else
  echo "Pod $pod_name is running normally."
fi
```
**Explanation**:  
- The script uses `kubectl` to check the status of a specific pod.
- If the pod is in a "CrashLoopBackOff" state, it deletes the pod, which will prompt Kubernetes to restart it automatically.
- This is useful for quickly addressing failing pods in your cluster.

---

### **3. Python Script to Automate AWS EC2 Instance Start/Stop**

**Purpose**: Automatically start or stop an AWS EC2 instance using Boto3.

```python
import boto3

# Define the instance ID and region
instance_id = 'i-1234567890abcdef0'
region = 'us-west-2'

# Create an EC2 client
ec2 = boto3.client('ec2', region_name=region)

# Start the EC2 instance
ec2.start_instances(InstanceIds=[instance_id])
print(f'Started EC2 instance {instance_id}')

# Stop the EC2 instance
# ec2.stop_instances(InstanceIds=[instance_id])
# print(f'Stopped EC2 instance {instance_id}')
```

**Explanation**:  
- This Python script uses the Boto3 library to start an EC2 instance in AWS.
- You can modify the script to stop the instance as needed by uncommenting the `stop_instances` line.
- It’s useful for automating the management of EC2 instances when they are not in use, helping reduce costs.

---

### **4. Bash Script to Clean Up Old Docker Images and Containers**

**Purpose**: Clean up unused Docker containers, images, and volumes to free up disk space.

```bash
#!/bin/bash
# Clean up unused Docker images, containers, and volumes

# Remove stopped containers
docker container prune -f

# Remove unused images
docker image prune -af

# Remove unused volumes
docker volume prune -f

# Remove unused networks
docker network prune -f

# Check Docker disk usage
docker system df
```

**Explanation**:  
- This script removes unused Docker containers, images, volumes, and networks.
- The `docker system df` command at the end gives a summary of Docker disk usage after cleanup.
- This is useful for keeping the system clean and freeing up disk space in a development or production environment.

---

### **5. Bash Script to Monitor CPU Usage and Send Alerts**

**Purpose**: Monitor CPU usage and send an email if the usage exceeds a certain threshold.

```bash
#!/bin/bash
# Monitor CPU usage and alert if it exceeds the threshold

cpu_threshold=80
current_cpu_usage=$(top -bn1 | grep "Cpu(s)" | sed "s/.*, *\([0-9.]*\)%* id.*/\1/" | awk '{print 100 - $1}')

if (( $(echo "$current_cpu_usage > $cpu_threshold" | bc -l) )); then
  echo "CPU usage is above $cpu_threshold%. Sending alert!"
  # Send an email or Slack notification
  echo "CPU usage is at ${current_cpu_usage}%." | mail -s "CPU Usage Alert" admin@example.com
fi
```

**Explanation**:  
- The script checks the CPU usage using the `top` command and calculates the percentage of CPU usage.
- If CPU usage exceeds the defined threshold (e.g., 80%), it sends an email notification using the `mail` command.
- This script can be set as a cron job to run periodically and ensure system health.

---

### **6. Bash Script to Update Kubernetes ConfigMap Values**

**Purpose**: Update values in a Kubernetes ConfigMap.

```bash
#!/bin/bash
# Update Kubernetes ConfigMap with new values

namespace="default"
configmap_name="my-configmap"
new_value="new_value"

# Update ConfigMap
kubectl patch configmap $configmap_name -n $namespace --patch "{\"data\": {\"my_key\": \"$new_value\"}}"

echo "ConfigMap $configmap_name updated with new value."
```

**Explanation**:  
- This script uses the `kubectl patch` command to update a key-value pair in a Kubernetes ConfigMap.
- The script updates the ConfigMap with a new value for the key `my_key`, and you can modify it to update other keys.

---

### **7. Python Script to Deploy a Dockerized App to AWS ECS**

**Purpose**: Deploy a Dockerized application to Amazon ECS.

```python
import boto3

ecs_client = boto3.client('ecs', region_name='us-west-2')

# Define ECS cluster and service
cluster_name = 'my-cluster'
service_name = 'my-service'

# Register new task definition (Docker container definition)
response = ecs_client.register_task_definition(
    family='my-task-family',
    containerDefinitions=[
        {
            'name': 'my-container',
            'image': 'my-docker-image:latest',
            'memory': 512,
            'cpu': 256,
            'essential': True,
        }
    ]
)

# Update ECS service to use the new task definition
ecs_client.update_service(
    cluster=cluster_name,
    service=service_name,
    taskDefinition=response['taskDefinition']['taskDefinitionArn']
)

print(f"Deployed new version to {service_name}")
```

**Explanation**:  
- This Python script registers a new ECS task definition for a Docker container and then updates the ECS service to use the newly registered task.
- It automates the process of deploying Dockerized applications to ECS, reducing the manual intervention required.

---

### **8. Bash Script to Automatically Rotate Logs**

**Purpose**: Automatically rotate logs in a Linux system.

```bash
#!/bin/bash
# Log Rotation Script

log_file="/var/log/myapp.log"
max_size=10000  # Max log file size in KB

log_size=$(du -k $log_file | cut -f1)

if [ $log_size -gt $max_size ]; then
  # Create a backup of the log file
  mv $log_file $log_file.$(date +'%Y%m%d%H%M%S').bak

  # Create a new empty log file
  touch $log_file
  echo "Log file rotated: $log_file"
fi
```

**Explanation**:  
- This script checks the size of the log file. If it exceeds the defined threshold, it rotates the log by renaming the existing file and creating a new empty log file.
- This can help keep log file sizes manageable and prevent them from consuming too much disk space.

---

### **9. Bash Script to Monitor Disk Usage and Send Alerts**

**Purpose**: Monitor disk space usage and alert if it exceeds a defined threshold.

```bash
#!/bin/bash
# Monitor disk space usage and alert if it exceeds the threshold

disk_usage_threshold=80
disk_usage=$(df / | grep / | awk '{ print $5 }' | sed 's/%//g')

if [ $disk_usage -gt $disk_usage_threshold ]; then
  echo "Disk space usage is above $disk_usage_threshold%. Sending alert!"
  echo "Disk usage is at $disk_usage%." | mail -s "Disk Space Alert" admin@example.com
fi
```

**Explanation**:  
- The script checks the disk usage using `df` and compares it to the defined threshold.
- If the usage exceeds the threshold, it sends an alert via email.

---

### **10. Bash Script to Deploy Helm Charts to Kubernetes**

**Purpose**: Automate the deployment of Helm charts to Kubernetes environments.

```bash
#!/bin/bash
# Deploy Helm charts to Kubernetes

chart_dir="/path/to/helm/charts"
release_name="my-release"
namespace="default"

# Install or upgrade the Helm release
helm upgrade --install $release_name $chart_dir --namespace $namespace

echo "Helm chart deployed to Kubernetes."
```

**Explanation**:  
- This script automates the deployment of a Helm chart to a Kubernetes cluster.
- It uses the `helm upgrade --install` command to either install or upgrade the release in the specified namespace.

---
