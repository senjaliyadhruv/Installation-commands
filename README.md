# DevOps Tools Installation & Configuration Guide 🚀

A comprehensive collection of installation commands and setup instructions for essential DevOps tools. This repository serves as a quick reference guide for setting up your development and production environments on **Ubuntu** and **Amazon Linux**.

---

## 📋 Table of Contents

- [AWS CLI, kubectl & eksctl Installation](#aws-cli-kubectl--eksctl-installation)
- [EKS Cluster Setup](#eks-cluster-setup)
- [Jenkins Installation & Configuration](#jenkins-installation--configuration)
- [Node.js Installation](#nodejs-installation)
- [Python Virtual Environment](#python-virtual-environment)
- [Terraform Installation](#terraform-installation)
- [Apache Tomcat](#apache-tomcat)
- [Maven Installation](#maven-installation)
- [Helm Installation](#helm-installation)
- [Trivy Security Scanner](#trivy-security-scanner)
- [ArgoCD Setup](#argocd-setup)
- [Prometheus & Grafana Monitoring](#prometheus--grafana-monitoring)
- [SonarQube Installation](#sonarqube-installation)
- [JFrog Artifactory](#jfrog-artifactory)

---

## aws-cli-kubectl--eksctl-installation

### Ubuntu

#### AWS CLI Installation
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip -y
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

#### kubectl Installation (Latest Stable Version)
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

#### eksctl Installation
```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

### Amazon Linux

#### AWS CLI Installation
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo yum install unzip -y
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

#### kubectl Installation (Latest Stable Version)
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

#### eksctl Installation
```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

---

## eks-cluster-setup

### Step 1: Create EKS Cluster
```bash
eksctl create cluster \
  --name=EKS-1 \
  --region=us-east-1 \
  --zones=us-east-1a,us-east-1b \
  --without-nodegroup
```

### Step 2: Associate OIDC Provider
Required for IAM roles for service accounts:
```bash
eksctl utils associate-iam-oidc-provider \
  --region us-east-1 \
  --cluster EKS-1 \
  --approve
```

### Step 3: Create Node Group
```bash
eksctl create nodegroup \
  --cluster=EKS-1 \
  --region=us-east-1 \
  --name=node2 \
  --node-type=t3.medium \
  --nodes=3 \
  --nodes-min=2 \
  --nodes-max=4 \
  --node-volume-size=20 \
  --ssh-access \
  --ssh-public-key=ec2 \
  --managed \
  --asg-access \
  --external-dns-access \
  --full-ecr-access \
  --appmesh-access \
  --alb-ingress-access
```

### Additional EKS Commands
```bash
# Create a simple cluster
eksctl create cluster \
  --name dhruv \
  --region us-east-1 \
  --node-type t2.medium

# Delete cluster
eksctl delete cluster dhruv --region us-east-1
```

---

## jenkins-installation--configuration

### Ubuntu

#### Step 1: Install Java
```bash
sudo apt update
sudo apt install openjdk-17-jdk -y
java -version
```

#### Step 2: Install Jenkins
```bash
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins -y
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

#### Step 3: Install Docker
```bash
sudo apt install docker.io -y
sudo usermod -aG docker jenkins
sudo usermod -aG docker $USER
sudo chmod 666 /var/run/docker.sock
sudo systemctl restart jenkins
```

#### Step 4: Access Jenkins
1. Open your browser and navigate to: `http://<your-ec2-public-ip>:8080`
2. Retrieve the initial admin password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
3. Complete the Jenkins setup wizard

#### Step 5: Install Required Plugins
Navigate to **Manage Jenkins → Plugins → Available Plugins** and install:
- Docker
- Docker Pipeline
- Kubernetes
- Kubernetes CLI

#### Step 6: Configure Jenkins Credentials
Go to **Manage Jenkins → Credentials** to add your credentials for:
- Docker Hub
- GitHub
- AWS
- Kubernetes

### Amazon Linux

#### Step 1: Install Java
```bash
sudo dnf install java-17-amazon-corretto -y
java -version
```

#### Step 2: Install Jenkins
```bash
sudo curl -o /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo dnf install jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```

#### Step 3: Install Docker
```bash
sudo yum install docker -y
sudo usermod -aG docker jenkins
sudo usermod -aG docker $USER
sudo systemctl start docker
sudo systemctl enable docker
sudo chmod 666 /var/run/docker.sock
sudo systemctl restart jenkins
```

#### Step 4: Access Jenkins
1. Open your browser and navigate to: `http://<your-ec2-public-ip>:8080`
2. Retrieve the initial admin password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
3. Complete the Jenkins setup wizard

#### Step 5: Install Required Plugins
Navigate to **Manage Jenkins → Plugins → Available Plugins** and install:
- Docker
- Docker Pipeline
- Kubernetes
- Kubernetes CLI

#### Step 6: Configure Jenkins Credentials
Go to **Manage Jenkins → Credentials** to add your credentials for:
- Docker Hub
- GitHub
- AWS
- Kubernetes

---

## nodejs-installation

### Amazon Linux / RHEL-based
```bash
# Update system
sudo yum update -y
# or
sudo dnf update -y

# Install Node.js 20.x LTS
curl -sL https://rpm.nodesource.com/setup_20.x | sudo -E bash -
sudo yum install -y nodejs
# or
sudo dnf install -y nodejs

# Verify installation
node --version
npm --version
```

### Ubuntu / Debian-based
```bash
# Update system
sudo apt update

# Install Node.js 20.x LTS
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs

# Verify installation
node --version
npm --version
```

---

## python-virtual-environment

### Ubuntu
```bash
# Install python3-venv if not already installed
sudo apt install -y python3-venv python3-pip

# Create virtual environment
python3 -m venv venv

# Activate virtual environment
source venv/bin/activate

# Deactivate when done
deactivate
```

### Amazon Linux
```bash
# Install python3 and pip
sudo yum install python3 python3-pip -y

# Create virtual environment
python3 -m venv venv

# Activate virtual environment
source venv/bin/activate

# Deactivate when done
deactivate
```

---

## terraform-installation

### Amazon Linux / RHEL-based
```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo
sudo yum -y install terraform

# Verify installation
terraform --version
```

### Ubuntu / Debian-based
```bash
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform -y

# Verify installation
terraform --version
```

---

## apache-tomcat

### Installation (Both Platforms)
```bash
# Download Tomcat (check for latest version at https://tomcat.apache.org/)
sudo wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.96/bin/apache-tomcat-9.0.96.tar.gz

# Extract archive
sudo tar -xvzf apache-tomcat-9.0.96.tar.gz

# Navigate to Tomcat directory
cd apache-tomcat-9.0.96/bin

# Make startup script executable
chmod +x startup.sh
chmod +x shutdown.sh

# Start Tomcat
./startup.sh

# Access Tomcat at http://<your-server-ip>:8080
```

### Using Docker (Recommended)
```bash
docker run -d \
  --name tomcat \
  -p 8089:8080 \
  tomcat:latest

# Access Tomcat at http://<your-server-ip>:8089
```

---

## maven-installation

### Amazon Linux / RHEL-based
```bash
sudo yum install maven -y
mvn --version
```

### Ubuntu / Debian-based
```bash
sudo apt update
sudo apt install maven -y
mvn --version
```

---

## helm-installation

### Both Platforms
```bash
# Download Helm (check for latest version at https://github.com/helm/helm/releases)
wget https://get.helm.sh/helm-v3.14.0-linux-amd64.tar.gz

# Extract archive
tar -zxvf helm-v3.14.0-linux-amd64.tar.gz

# Move to PATH
sudo mv linux-amd64/helm /usr/local/bin/helm

# Set permissions
sudo chmod 755 /usr/local/bin/helm

# Verify installation
helm version
```

---

## trivy-security-scanner

### Amazon Linux / RHEL-based
```bash
# Install latest Trivy (check https://github.com/aquasecurity/trivy/releases)
sudo rpm -ivh https://github.com/aquasecurity/trivy/releases/download/v0.58.0/trivy_0.58.0_Linux-64bit.rpm

# Verify installation
trivy --version
```

### Ubuntu / Debian-based
```bash
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy -y

# Verify installation
trivy --version
```

---

## argocd-setup

### Installation
```bash
# Create namespace
kubectl create namespace argocd

# Install ArgoCD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Wait for pods to be ready
kubectl wait --for=condition=ready pod -l app.kubernetes.io/name=argocd-server -n argocd --timeout=300s
```

### Enable NodePort Access
```bash
# Edit the argocd-server service to change type to NodePort
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'

# Get the NodePort
kubectl get svc argocd-server -n argocd

# Alternative: Use kubectl edit
kubectl edit svc argocd-server -n argocd
# Change 'type: ClusterIP' to 'type: NodePort' and save
```

### Retrieve Admin Password
```bash
# Get the initial admin password
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode && echo

# Alternative method
kubectl get secret argocd-initial-admin-secret -n argocd -o yaml
# Then decode the password manually:
echo "<password-value>" | base64 --decode
```

### Access ArgoCD
- **Username:** `admin`
- **Password:** Retrieved from the command above
- **URL:** `https://<node-ip>:<nodeport>`

---

## prometheus--grafana-monitoring

### Installation using Helm
```bash
# Add Helm repositories
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add stable https://charts.helm.sh/stable
helm repo update

# Create namespace
kubectl create namespace prometheus

# Install kube-prometheus-stack (includes Prometheus, Grafana, and Alertmanager)
helm install stable prometheus-community/kube-prometheus-stack -n prometheus

# Verify installation
kubectl get pods -n prometheus

# Check all resources
kubectl get all -n prometheus
```

### Access Grafana
```bash
# Get Grafana admin password
kubectl get secret -n prometheus stable-grafana -o jsonpath="{.data.admin-password}" | base64 --decode && echo

# Port forward to access Grafana locally
kubectl port-forward -n prometheus svc/stable-grafana 3000:80

# Or expose via NodePort
kubectl patch svc stable-grafana -n prometheus -p '{"spec": {"type": "NodePort"}}'
kubectl get svc stable-grafana -n prometheus

# Access at http://localhost:3000 (if port-forwarded)
# Or http://<node-ip>:<nodeport> (if NodePort)
# Username: admin
# Password: Retrieved from above command
```

### Access Prometheus
```bash
# Port forward to access Prometheus locally
kubectl port-forward -n prometheus svc/stable-kube-prometheus-sta-prometheus 9090:9090

# Or expose via NodePort
kubectl patch svc stable-kube-prometheus-sta-prometheus -n prometheus -p '{"spec": {"type": "NodePort"}}'
kubectl get svc stable-kube-prometheus-sta-prometheus -n prometheus

# Access at http://localhost:9090 (if port-forwarded)
# Or http://<node-ip>:<nodeport> (if NodePort)
```

---

## sonarqube-installation

### Amazon Linux / RHEL-based
```bash
# Install dependencies
sudo yum -y install wget nfs-utils

# Add SonarQube repository
sudo wget -O /etc/yum.repos.d/sonar.repo http://downloads.sourceforge.net/project/sonar-pkg/rpm/sonar.repo

# Install SonarQube
sudo yum -y install sonar

# Start SonarQube
sudo systemctl start sonar
sudo systemctl enable sonar
sudo systemctl status sonar

# Access SonarQube at http://<your-server-ip>:9000
# Default credentials - Username: admin, Password: admin
```

### Using Docker (Recommended - Both Platforms)
```bash
docker run -d \
  --name sonarqube \
  -p 9000:9000 \
  sonarqube:lts-community

# Check logs
docker logs -f sonarqube

# Access SonarQube at http://<your-server-ip>:9000
# Default credentials - Username: admin, Password: admin
```

---

## jfrog-artifactory

### Amazon Linux / RHEL-based
```bash
# Download and add JFrog repository
sudo wget https://releases.jfrog.io/artifactory/artifactory-rpms/artifactory-rpms.repo -O jfrog-artifactory-rpms.repo
sudo mv jfrog-artifactory-rpms.repo /etc/yum.repos.d/

# Install JFrog Artifactory OSS
sudo yum update && sudo yum install jfrog-artifactory-oss -y

# Start Artifactory service
sudo systemctl start artifactory.service
sudo systemctl enable artifactory.service

# Check status
sudo systemctl status artifactory.service

# Access Artifactory at http://<your-server-ip>:8081
# Default credentials - Username: admin, Password: password
```

### Using Docker (Recommended - Both Platforms)
```bash
# Create directories for Artifactory data
mkdir -p $HOME/artifactory/data

# Run Artifactory container
docker run -d \
  --name artifactory \
  -p 8081:8081 \
  -p 8082:8082 \
  -v $HOME/artifactory/data:/var/opt/jfrog/artifactory \
  releases-docker.jfrog.io/jfrog/artifactory-oss:latest

# Check logs
docker logs -f artifactory

# Access Artifactory at http://<your-server-ip>:8081
# Default credentials - Username: admin, Password: password
```

---

## 📝 Additional Useful Commands

### Docker Commands
```bash
# Check Docker version
docker --version

# List running containers
docker ps

# List all containers
docker ps -a

# Stop a container
docker stop <container-name>

# Remove a container
docker rm <container-name>

# View container logs
docker logs <container-name>

# Execute command in running container
docker exec -it <container-name> /bin/bash
```

### Kubernetes Commands
```bash
# Get cluster info
kubectl cluster-info

# Get all resources in all namespaces
kubectl get all -A

# Get nodes
kubectl get nodes

# Describe a resource
kubectl describe pod <pod-name> -n <namespace>

# Get logs from a pod
kubectl logs <pod-name> -n <namespace>

# Delete a resource
kubectl delete pod <pod-name> -n <namespace>

# Apply a configuration file
kubectl apply -f <file.yaml>

# Get services
kubectl get svc -A
```

### AWS CLI Commands
```bash
# Configure AWS CLI
aws configure

# List S3 buckets
aws s3 ls

# List EC2 instances
aws ec2 describe-instances

# List EKS clusters
aws eks list-clusters --region us-east-1

# Update kubeconfig for EKS
aws eks update-kubeconfig --region us-east-1 --name <cluster-name>
```

---

## 🔐 Security Best Practices

1. **Always use IAM roles** instead of hardcoding credentials
2. **Enable MFA** for AWS root and IAM users
3. **Use security groups** to restrict access to only necessary ports
4. **Keep systems updated** with the latest security patches
5. **Use secrets management** tools like AWS Secrets Manager or HashiCorp Vault
6. **Implement least privilege access** for all users and services
7. **Enable logging and monitoring** for all critical services
8. **Use HTTPS/TLS** for all web services
9. **Regularly scan** for vulnerabilities using tools like Trivy
10. **Backup critical data** regularly

---

## 🚨 Common Troubleshooting

### Jenkins not starting
```bash
# Check Jenkins status
sudo systemctl status jenkins

# Check Jenkins logs
sudo journalctl -u jenkins -f

# Restart Jenkins
sudo systemctl restart jenkins
```

### Docker permission denied
```bash
# Add user to docker group
sudo usermod -aG docker $USER

# Reload group membership
newgrp docker

# Or logout and login again
```

### kubectl connection issues
```bash
# Update kubeconfig
aws eks update-kubeconfig --region <region> --name <cluster-name>

# Verify connection
kubectl cluster-info

# Check kubectl config
kubectl config view
```

### EKS Node Group not ready
```bash
# Check node status
kubectl get nodes

# Describe nodes for issues
kubectl describe nodes

# Check AWS console for node group status
aws eks describe-nodegroup --cluster-name <cluster-name> --nodegroup-name <nodegroup-name> --region <region>
```

---

## 📚 Useful Resources

- [AWS Documentation](https://docs.aws.amazon.com/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Jenkins Documentation](https://www.jenkins.io/doc/)
- [Docker Documentation](https://docs.docker.com/)
- [Terraform Documentation](https://www.terraform.io/docs/)
- [Helm Documentation](https://helm.sh/docs/)
- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [Prometheus Documentation](https://prometheus.io/docs/)
- [Grafana Documentation](https://grafana.com/docs/)

---

## 🤝 Contributing

Feel free to submit issues and enhancement requests! Contributions are always welcome.

### How to Contribute
1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---


**Happy DevOps! 🚀**
