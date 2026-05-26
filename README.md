# 🚀 Boutique_App — Production Grade DevOps Project

## 🌐 Live Project

### 🔗 Application URL
https://ovaisportfolio.in

### 📊 Grafana Monitoring Dashboard
http://ad4f099ab5e814da5adeedfce7cf9009-1832564091.ap-northeast-1.elb.amazonaws.com

---

# 📌 Project Overview

Boutique_App is a complete end-to-end Production-Style DevOps implementation of a cloud-native microservices-based e-commerce application deployed on AWS EKS using Kubernetes, Docker, Jenkins CI/CD, Terraform, Prometheus, Grafana, Route53, ACM SSL, and AWS ALB Ingress.

This project automates:

- Infrastructure provisioning
- Docker image management
- CI/CD deployment
- Kubernetes orchestration
- HTTPS routing
- Domain management
- Monitoring & observability

---

# 🏗️ Complete Architecture Diagram

![Architecture Diagram](./Outputs/architecture-diagram.png)

---

# ⚡ Tech Stack Used

## ☁️ Cloud & Infrastructure
- AWS EC2
- AWS EKS
- AWS ECR
- AWS Route53
- AWS ACM
- AWS ALB

## ⚙️ DevOps Tools
- Terraform
- Jenkins
- Docker
- Kubernetes
- Helm
- GitHub

## 📊 Monitoring
- Prometheus
- Grafana

## 🔐 Networking & Security
- HTTPS SSL
- Kubernetes Ingress
- AWS Load Balancer Controller

---

# 🔥 Features

✅ Fully containerized microservices architecture  
✅ Infrastructure as Code using Terraform  
✅ CI/CD automation with Jenkins  
✅ Kubernetes orchestration on AWS EKS  
✅ HTTPS-enabled custom domain  
✅ Real-time monitoring dashboards  
✅ AWS ALB Ingress integration  
✅ Automated Docker image management  
✅ Production-style deployment architecture  

---

# 📂 Project Structure

```bash
Boutique_App/
│
├── EKS-cluster-terraform/
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
│
├── Microservices/
│   ├── src/
│   ├── kubernetes-manifests/
│   ├── helm-chart/
│   ├── ingress.yaml
│   └── docker_image_buid_push.sh
│
├── Jenkinsfile
├── Monitor.sh
├── Outputs/
└── README.md
```

---

# 🚀 Project Execution Steps

# PHASE 1 — AWS Infrastructure Setup

## 1️⃣ Create EC2 Instances

Create 2 EC2 Instances:

| Server | Purpose |
|---|---|
| Jump Server | Kubernetes Control Machine |
| Jenkins Server | CI/CD Automation |

### Recommended Region
```bash
ap-northeast-1 (Tokyo)
```

### Recommended Instance Types
```bash
c7i-flex.large
```

---

## 2️⃣ Install Required Tools

### 🔹 Jump Server

Install:
- AWS CLI
- kubectl
- eksctl
- terraform
- helm

### 🔹 Jenkins Server

Install:
- Java
- Jenkins
- Docker
- kubectl
- AWS CLI

---

# PHASE 2 — EKS Cluster Setup

## Navigate to Terraform Folder

```bash
cd Boutique_App/EKS-cluster-terraform
```

## Initialize Terraform

```bash
terraform init
```

## Preview Infrastructure

```bash
terraform plan
```

## Create AWS Infrastructure

```bash
terraform apply
```

Type:

```bash
yes
```

---

## Verify Cluster

```bash
aws eks list-clusters --region ap-northeast-1
```

```bash
kubectl get nodes
```

---

# PHASE 3 — Docker Build & ECR Push

## Update AWS Account ID

Update inside:

```bash
Microservices/docker_image_buid_push.sh
```

Replace:

```bash
AWS_ACCOUNT_ID="YOUR_ACCOUNT_ID"
```

---

## Build & Push Images

```bash
cd Boutique_App/Microservices
```

```bash
chmod +x docker_image_buid_push.sh
```

```bash
./docker_image_buid_push.sh
```

This will:
- Build Docker images
- Push images to AWS ECR

---

# PHASE 4 — Kubernetes Deployment

## Deploy Application

```bash
kubectl apply -f Microservices/kubernetes-manifests
```

---

## Verify Pods

```bash
kubectl get pods
```

---

## Verify Services

```bash
kubectl get svc
```

---

# PHASE 5 — Jenkins CI/CD Setup

## Configure Jenkins

### Add Credentials:
- AWS Credentials
- GitHub Access

---

## Jenkins Pipeline Flow

Pipeline automatically:

1. Pulls latest code from GitHub
2. Builds Docker images
3. Pushes images to ECR
4. Deploys to EKS
5. Verifies deployment

---

# 📦 Jenkins Pipeline Stages

```text
Checkout Code
↓
Build Docker Images
↓
Push Images to ECR
↓
Deploy to Kubernetes
↓
Verify Deployment
```

---

# PHASE 6 — Monitoring Setup

# 📊 Install Prometheus

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

```bash
helm repo update
```

```bash
kubectl create namespace monitoring
```

```bash
helm install prometheus prometheus-community/prometheus \
-n monitoring \
--set server.persistentVolume.enabled=false \
--set alertmanager.enabled=false \
--set pushgateway.enabled=false
```

---

# 📈 Install Grafana

```bash
helm install grafana grafana/grafana -n monitoring
```

---

## Expose Grafana

```bash
kubectl expose service grafana \
-n monitoring \
--type=LoadBalancer \
--target-port=3000 \
--name=grafana-external
```

---

## Get Grafana Password

```bash
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

---

# 📊 Grafana Dashboard

Dashboard ID used:

```bash
315
```

Features:
- Kubernetes Node Metrics
- Pod Monitoring
- CPU Usage
- Memory Usage
- Cluster Health

---

# PHASE 7 — Domain Setup

# 🌐 Route53 + GoDaddy Setup

## Create Route53 Hosted Zone

Domain Used:

```bash
ovaisportfolio.in
```

---

## Update GoDaddy Nameservers

Replace GoDaddy nameservers with Route53 nameservers.

---

## Create Route53 A Record

Point domain to:
- AWS ALB Ingress Load Balancer

---

# PHASE 8 — HTTPS SSL Setup

# 🔐 Create ACM SSL Certificate

- Create ACM Certificate
- DNS Validation
- Route53 Validation Record

---

# Install AWS Load Balancer Controller

```bash
eksctl utils associate-iam-oidc-provider \
--region ap-northeast-1 \
--cluster boutique-eks-cluster \
--approve
```

---

```bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.13.3/docs/install/iam_policy.json
```

---

```bash
aws iam create-policy \
--policy-name AWSLoadBalancerControllerIAMPolicy \
--policy-document file://iam_policy.json
```

---

```bash
eksctl create iamserviceaccount \
--cluster=boutique-eks-cluster \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--role-name AmazonEKSLoadBalancerControllerRole \
--attach-policy-arn=arn:aws:iam::YOUR_ACCOUNT_ID:policy/AWSLoadBalancerControllerIAMPolicy \
--approve \
--region ap-northeast-1
```

---

```bash
helm repo add eks https://aws.github.io/eks-charts
```

```bash
helm repo update
```

---

```bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
-n kube-system \
--set clusterName=boutique-eks-cluster \
--set serviceAccount.create=false \
--set serviceAccount.name=aws-load-balancer-controller \
--set region=ap-northeast-1
```

---

# Create Kubernetes Ingress

```bash
kubectl apply -f ingress.yaml
```

---

# 🔒 HTTPS Flow

```text
Browser
   ↓
HTTPS (443)
   ↓
AWS ALB
   ↓
Kubernetes Ingress
   ↓
Frontend Service
   ↓
Pods
```

---

# 📈 Monitoring Workflow

```text
Kubernetes Cluster
        ↓
Prometheus Collects Metrics
        ↓
Grafana Visualizes Dashboards
```

---

# ⚙️ CI/CD Workflow

```text
Developer Pushes Code
        ↓
GitHub Repository
        ↓
Jenkins Pipeline Trigger
        ↓
Docker Image Build
        ↓
Push Images to AWS ECR
        ↓
Deploy to Kubernetes (EKS)
        ↓
Application Updated
```

---

# 🧠 Key Learnings

- Kubernetes Cluster Management
- Infrastructure as Code
- Docker Containerization
- CI/CD Automation
- HTTPS Ingress Routing
- Monitoring & Observability
- AWS Cloud Architecture
- Load Balancer Configuration
- Troubleshooting Kubernetes Workloads

---

# 🚀 Future Improvements

- ArgoCD GitOps
- SonarQube Integration
- Trivy Image Scanning
- Loki Logging Stack
- Horizontal Pod Autoscaler
- Blue-Green Deployment
- Multi-Environment Deployment

---

# 👨‍💻 Author

## Mohammed Ovais

### 🌐 Portfolio
https://ovaisportfolio.in

### 💻 GitHub
https://github.com/muhmdOvais

### 📦 Project Repository
https://github.com/muhmdOvais/Boutique_App

---

# ⭐ Final Outcome

This project demonstrates a complete production-style DevOps implementation including:

✅ Infrastructure Provisioning  
✅ Kubernetes Orchestration  
✅ CI/CD Automation  
✅ Docker Containerization  
✅ AWS Cloud Deployment  
✅ HTTPS SSL Routing  
✅ Monitoring & Observability  
✅ Domain Management  
✅ Production Networking  

---
