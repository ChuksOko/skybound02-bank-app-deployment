# 🏦 Skybound02 — Bank App Deployment on AWS EKS

> A team capstone project deploying a full-stack bank application on AWS Elastic Kubernetes Service (EKS) using a complete GitOps CI/CD pipeline. The project integrates GitHub Actions, ArgoCD, Terraform, NGINX Ingress, and Route 53 to deliver a production-grade, publicly accessible application at `bank.skybound02.online`.

---

## 📌 Project Overview

| | |
|---|---|
| **Project Name** | Skybound02 Bank App Deployment |
| **Domain** | `skybound02.online` |
| **Live Frontend** | `https://bank.skybound02.online` |
| **Live Backend API** | `https://bankapi.skybound02.online` |
| **ArgoCD Dashboard** | `https://argocd.skybound02.online` |
| **Platform** | AWS EKS (Elastic Kubernetes Service) |
| **Project Tracking** | Jira (SKYBOUND sprints) |
| **Timeline** | June–July 2025 |

---

## 👤 My Roles in This Project

### 1. Architecture Team — Co-designer
Designed the microservice and infrastructure architecture for the full deployment alongside the architecture team. Responsible for defining how the frontend, backend, and database components interact, how traffic flows from DNS through the load balancer to Kubernetes services, and how ArgoCD fits into the GitOps model.

**Architecture team members:** Chukwuemeka Oko · Chioma Ononyaba · Blessing Nkemakolam · Vivian Ashakah · Daniel Okwute

### 2. AWS Team — Domain & DNS Configuration
Assigned and completed **SKYBOUND-12**: configured DNS records on Namecheap to point to the correct AWS server, added A record and CNAME for subdomain, and verified setup via DNS lookup.

### 3. DevOps Team — CI/CD Pipeline Documentation
Authored the complete DevOps workflow documentation covering the end-to-end CI/CD pipeline from code push through GitHub Actions, GHCR, ArgoCD sync, Kubernetes deployment, and public access via NGINX Ingress.

---

## 🏗️ Architecture Overview

The application is split into three containerised components:

```
Frontend (React)  →  frontendapp namespace
Backend (Node/Express API)  →  backendapp namespace
Database (MySQL)  →  database namespace
```

All components are managed via Kubernetes on AWS EKS, deployed using GitOps principles through ArgoCD.

![Architecture Diagram](screenshots/architecture.jpg)

### Traffic Flow

```
User Browser
    ↓
https://bank.skybound02.online
    ↓
Route 53 (DNS resolution)
    ↓
AWS Network Load Balancer (NLB)
    ↓
NGINX Ingress Controller (EKS)
    ↓
├── /      → Frontend Service (frontendapp namespace)
└── /api   → Backend Service (backendapp namespace)
```

---

## ⚙️ CI/CD Pipeline — Full Workflow

**Trigger → CI (GitHub Actions) → GHCR → CD (ArgoCD) → Kubernetes → Public Access**

### Step 1 — Developer Pushes Code

- **Frontend code** pushed to Repo A
- **Backend code** pushed to Repo B
- Both repos connected to GitHub Actions workflows

### Step 2 — GitHub Actions (CI Pipeline)

On push to `main`, GitHub Actions:
- Builds the Docker image from the Dockerfile in each repo root
- Tags the image using commit SHA or version number
- Pushes the image to **GitHub Container Registry (GHCR)**

```yaml
name: Build and Push Frontend
on:
  push:
    branches: [ main ]
jobs:
  build:
    runs-on: linux
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Build Docker image
        run: docker build -t ghcr.io/org/frontend:latest .
      - name: Login to GHCR
        run: echo "${{ secrets.GHCR_PAT }}" | docker login ghcr.io -u USERNAME --password-stdin
      - name: Push to GHCR
        run: docker push ghcr.io/org/frontend:latest
```

### Step 3 — GitOps Deployment with ArgoCD

- Deployment manifests stored in **Repo C** (Kubernetes YAMLs)
- ArgoCD watches Repo C and automatically syncs changes to EKS
- Apps deployed to their respective namespaces: `frontendapp`, `backendapp`

### Step 4 — Kubernetes Resources Applied

- Deployments, Services, and Ingresses created/updated
- Docker images pulled from GHCR
- React frontend → `frontendapp` namespace
- Node/Express API → `backendapp` namespace

### Step 5 — Public Access

| URL | Routes To |
|---|---|
| `https://bank.skybound02.online` | Frontend (React App) |
| `https://bankapi.skybound02.online` | Backend API |
| `https://argocd.skybound02.online` | ArgoCD Dashboard |

---

## ☁️ AWS Infrastructure Setup

### Resources Provisioned

| Resource | Purpose |
|---|---|
| **EKS Cluster** | Hosts all Kubernetes workloads |
| **EC2 Worker Nodes** | Run containers (t2.medium, t3.medium, t3.large) |
| **Network Load Balancer (NLB)** | External traffic entry point |
| **Route 53** | DNS management for `skybound02.online` |
| **S3 Bucket (skybound02)** | Terraform remote state storage |
| **DynamoDB Table (terraform-lock)** | Terraform state locking |
| **IAM Users, Groups & Roles** | Least-privilege access control |
| **VPC, Subnets, Security Groups** | Network isolation |
| **GHCR** | Container image registry |
| **Google Workspace** | Organisational email (`support@skybound02.online`) |

### Terraform Backend Configuration

```hcl
terraform {
  backend "s3" {
    bucket         = "skybound02"
    key            = "s3-cicd/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-lock"
    encrypt        = true
  }
}
```

---

## 🔐 Access & Authentication

| Tool | URL | Auth |
|---|---|---|
| ArgoCD | `https://argocd.skybound02.online` | Username / Password |
| Frontend | `https://bank.skybound02.online` | Public |
| Backend API | `https://bankapi.skybound02.online` | Token / App Auth |

---

## 📋 Jira Task Tracking (SKYBOUND Sprint)

Project managed on Jira with sprint-based task assignment, epics, and Slack integration.

| Ticket | Task | Assignee | Status |
|---|---|---|---|
| SKYBOUND-12 | Namecheap Domain Setup | **Oko Chukwuemeka** | ✅ Done |
| SKYBOUND-13 | Open AWS Free Tier Account | Anthony | ✅ Done |
| SKYBOUND-14 | Create Users & Assign Roles | Anthony | ✅ Done |
| SKYBOUND-15 | Setup Organisational Email (Google Workspace) | Anthony | ✅ Done |
| SKYBOUND-16 | Create & Setup S3 Bucket | Anuoluwapo | ✅ Done |
| SKYBOUND-17 | Launch EC2 Instance | Anuoluwapo | ✅ Done |
| SKYBOUND-18 | Create DynamoDB Table | Anuoluwapo | ✅ Done |
| SKYBOUND-19 | Connect AWS DNS to Namecheap | Anthony, Judith | ✅ Done |
| SKYBOUND-20 | Launch Additional EC2 Instances | Anthony, Judith | ✅ Done |

**My Jira task:** SKYBOUND-12 — Configured DNS records on Namecheap to point to the correct server. Added A record and CNAME for subdomain. Setup verified via DNS lookup.

---

## 🐛 Challenges & Resolutions

### 1. Terraform S3 Backend Error

**Error:** `No valid credential sources found for AWS Provider`

**Cause:** AWS credentials not configured in the GitHub Actions runner.

**Resolution:** Updated the Terraform backend configuration with correct S3 bucket, key, region, and DynamoDB table references. Added AWS credentials to GitHub Actions secrets.

---

### 2. Terraform Validation Error — helm_release

**Cause:** `helm_release` resource does not support a `set {}` block — misunderstood syntax.

**Resolution:** Corrected `addons.tf` to use `set = [{ name = "installCRDs" value = "true" }]` instead of a nested block.

---

### 3. ECR Authentication Error

**Error:** `aws: command not found` · `Cannot perform an interactive login from a non TTY device`

**Cause:** AWS CLI not installed on runner; `docker login` failed in non-interactive environment.

**Resolution:** Added AWS CLI installation step and used `get-login-password` for non-interactive Docker login:

```bash
- name: Install AWS CLI
  run: |
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    unzip awscliv2.zip
    sudo ./aws/install
    aws --version
```

---

### 4. Kubernetes Manifest Checkout Error

**Cause:** Incorrect or missing Amazon ECR image references in `frontend.yaml` and `backendapi.yaml`.

**Resolution:** Updated image references in both manifests to point to the correct ECR image path under the account.

---

### 5. MySQL Database Connection Error

**Cause:** Incorrect `spring.datasource.url` in `application.properties` — wrong hostname, port, or database name.

**Resolution:** Updated the database URL configuration in the backend's `application.properties` to correctly point to the running MySQL instance.

---

## 📚 Lessons Learned

- Reading error logs carefully and tracing back to the root cause is the most efficient debugging approach
- Automation via Terraform and GitHub Actions dramatically reduces manual configuration errors
- Passing secrets securely in CI/CD pipelines requires deliberate setup — not an afterthought
- Cross-functional team coordination (Architecture, AWS, DevOps) requires clear documentation to prevent duplicated effort
- Small, testable changes during debugging are more effective than large iterative fixes

---

## 🧰 Full Technology Stack

| Category | Tools |
|---|---|
| **Container Orchestration** | AWS EKS · Kubernetes |
| **CI Pipeline** | GitHub Actions |
| **CD / GitOps** | ArgoCD |
| **Infrastructure as Code** | Terraform |
| **Container Registry** | GitHub Container Registry (GHCR) |
| **Ingress** | NGINX Ingress Controller |
| **DNS** | AWS Route 53 · Namecheap |
| **Load Balancing** | AWS Network Load Balancer (NLB) |
| **State Management** | S3 · DynamoDB |
| **Project Management** | Jira · Slack |
| **Email** | Google Workspace |
| **SSH Access** | MobaXterm |

---

## 👥 Team

| Team | Members |
|---|---|
| **Architecture** | Chukwuemeka Oko · Chioma Ononyaba · Blessing Nkemakolam · Vivian Ashakah · Daniel Okwute |
| **AWS** | Anuoluwapo Oni · Judith Omorogbe · Anthony |
| **DevOps** | (CI/CD pipeline documentation — Chukwuemeka Oko) |

---

## 📁 Repository Structure

```
📦 skybound02-bank-app-deployment/
├── 📄 README.md
├── 📄 index.html
└── 📁 screenshots/
    ├── architecture.jpg
    ├── cicd-pipeline.jpg
    ├── argocd-dashboard.jpg
    ├── live-app.jpg
    └── jira-board.jpg
```

---

## ⚠️ Disclaimer

> This project was completed as a capstone exercise. All infrastructure was provisioned in a controlled AWS environment. Domain and cloud resources used solely for project demonstration purposes.
