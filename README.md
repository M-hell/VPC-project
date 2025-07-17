# AWS Cloud Infrastructure with CI/CD Pipeline

![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-%232088FF.svg?style=for-the-badge&logo=github-actions&logoColor=white)
![Terraform](https://img.shields.io/badge/Terraform-%235835CC.svg?style=for-the-badge&logo=terraform&logoColor=white)

## 📌 Project Overview

This project implements a **highly available, auto-scaling web infrastructure** on AWS with a fully automated CI/CD pipeline. The architecture combines:

- **Custom VPC** with public/private subnet isolation
- **Auto-scaling EC2 instances** behind an Application Load Balancer
- **Static website hosting** via S3 with automated deployments
- **GitHub Actions CI/CD pipeline** for infrastructure and application updates

Key benefits:
✔ **Fault tolerance** through multi-AZ deployment  
✔ **Automatic scaling** based on traffic demands  
✔ **Secure architecture** with private instances and public ALB  
✔ **Zero-downtime deployments** via CI/CD automation  

---

## 🌐 Architecture Diagram

*(Diagram placeholder - to be updated with actual architecture visualization)*

```
[VPC] → [Internet Gateway]
    ├── [Public Subnet] → [ALB] → [Target Group]
    │                       │
    │                       ↓
    └── [Private Subnet] ← [NAT Gateway] ← [Auto Scaling Group (EC2)]
                                ↑
                          [S3 Static Website] ← [GitHub Actions CI/CD]
```

---

## 🛠 AWS Services Deep Dive

### 1. Virtual Private Cloud (VPC)
**Purpose**: Isolated network environment with controlled access

**Implementation**:
- **CIDR Block**: `10.0.0.0/16` (65,536 IP addresses)
- **Subnets**:
  - Public (`10.0.1.0/24`, `10.0.2.0/24`) - Internet-facing resources
  - Private (`10.0.3.0/24`, `10.0.4.0/24`) - Backend instances
- **Route Tables**:
  - Public: Routes `0.0.0.0/0` → Internet Gateway
  - Private: Routes `0.0.0.0/0` → NAT Gateway
- **Security**: Network ACLs and Security Groups for layered protection

### 2. EC2 Auto Scaling
**Purpose**: Automatically adjust compute capacity based on demand

**Configuration**:
- **Launch Template**:
  - AMI: Amazon Linux 2023
  - Instance Type: `t3.micro` (burstable CPU)
  - User Data: Bootstrap script for web server installation
  ```bash
  #!/bin/bash
  yum update -y
  yum install -y httpd
  systemctl start httpd
  systemctl enable httpd
  ```

- **Auto Scaling Policies**:
  - Scale-out: CPU > 70% for 5 minutes
  - Scale-in: CPU < 30% for 10 minutes
  - Health Check Type: ELB (combines EC2 and ALB health checks)

### 3. Application Load Balancer (ALB)
**Purpose**: Distribute traffic across healthy instances

**Setup**:
- **Listeners**: HTTP (80) → Target Group
- **Health Checks**: HTTP GET `/` (200 OK)
- **Routing**:
  - Path-based routing (future-proof for microservices)
  - Sticky sessions (if needed)
- **Security**: TLS termination (ACM certificate)

### 4. Amazon S3 Static Website
**Purpose**: Host frontend assets with high availability

**Configuration**:
- **Bucket Policy** (Example):
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```
- **Properties**:
  - Static website hosting enabled
  - Index document: `index.html`
  - Error document: `404.html`
- **URL**: `http://your-bucket-name.s3-website-region.amazonaws.com`

### 5. CI/CD Pipeline (GitHub Actions)
**Purpose**: Automate infrastructure and application deployments

**Workflow**:
```yaml
name: Deploy to S3
on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1
    - run: aws s3 sync ./ s3://your-bucket-name --delete
```

**Secrets Required**:
- `AWS_ACCESS_KEY_ID` - IAM user with S3 write permissions
- `AWS_SECRET_ACCESS_KEY` - Corresponding secret key

---

## ⚙️ Deployment Guide

### Prerequisites
- AWS account with admin permissions
- GitHub repository with code
- AWS CLI configured locally

### Step 1: VPC Setup
1. Create VPC with IPv4 CIDR block
2. Create public/private subnets across 2 AZs
3. Configure Internet Gateway and NAT Gateway
4. Set up route tables with proper routes

### Step 2: EC2 & Auto Scaling
1. Create Launch Template with user data script
2. Configure Auto Scaling Group:
   - Min: 2 instances
   - Max: 6 instances
   - Desired: 2 instances
3. Attach to Target Group

### Step 3: ALB Configuration
1. Create Application Load Balancer
2. Configure listener rules
3. Set health check parameters
4. Verify traffic routing

### Step 4: S3 & CI/CD
1. Create S3 bucket with public access
2. Configure GitHub Secrets with AWS credentials
3. Commit GitHub Actions workflow file
4. Verify automatic deployment on push

---

## 🔄 CI/CD Workflow Details

**Trigger**: Code push to `main` branch  
**Process**:
1. GitHub Actions runner checks out code
2. AWS credentials configured via OIDC or secrets
3. AWS CLI syncs files to S3 bucket (`--delete` removes obsolete files)
4. CloudFront cache invalidated (if used)

**Security Best Practices**:
- Use IAM roles with least privilege
- Rotate credentials regularly
- Enable workflow approval for production

---

## 📸 Future Diagrams (Placeholders)
- [ ] VPC Flow Diagram
- [ ] ALB Routing Logic
- [ ] CI/CD Pipeline Stages
- [ ] Auto Scaling Decision Flow

---

## 🌐 Live Demo
*(URL will be added post-deployment)*

---

## 📜 License
MIT License - See [LICENSE](LICENSE) for details.

## 🛠 Technologies Used
- AWS EC2, VPC, ALB, S3
- GitHub Actions
- Terraform (if applicable)
- Amazon Linux

## 👥 Contributors
[Your Name/Team] - Initial implementation
```

---

### Professional Touches Included:
1. **Badges** - Visual indicators for technologies
2. **Diagram Placeholder** - Clear structure for future visuals
3. **Code Blocks** - Properly formatted commands and configurations
4. **Section Icons** - Visual cues for different sections
5. **Detailed Explanations** - Each AWS service gets thorough coverage
6. **Step-by-Step Deployment** - Reproducible instructions
7. **Security Notes** - Best practices highlighted

To use this:
1. Copy into your `README.md`
2. Replace placeholder values (bucket names, IP ranges etc.)
3. Add actual diagrams when available
4. Update license/contributors as needed
