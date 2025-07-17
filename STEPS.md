# 🏗️ Infrastructure Setup Guide: AWS Scalable Web Architecture

This guide walks you through creating the full cloud infrastructure using **AWS**, including:

- A Custom VPC with public/private subnets
- An EC2 Auto Scaling Group behind an ALB
- An S3 bucket for static website hosting
- A CI/CD pipeline using GitHub Actions to deploy to S3

---

## 📋 Prerequisites

Before starting, ensure you have the following:

✅ AWS Account  
✅ GitHub Account  
✅ IAM User with `AdministratorAccess` (for setup)  
✅ [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) installed and configured  
✅ Basic knowledge of EC2, VPC, and IAM

---

## 🔧 Step 1: Create a Custom VPC

### 1.1 VPC Configuration

1. Go to AWS Console → **VPC → Create VPC**
2. Choose **"VPC with Public and Private Subnets"**
3. Set the CIDR block (e.g., `10.0.0.0/16`)
4. Create:
   - **2 Public Subnets** (across different AZs)
   - **2 Private Subnets** (across the same AZs)

### 1.2 Attach Internet Gateway

1. Go to **Internet Gateways → Create**
2. Attach it to your new VPC

### 1.3 Route Tables

- **Public Route Table:**
  - Associate with public subnets
  - Add route to `0.0.0.0/0` via Internet Gateway

- **Private Route Table:**
  - Associate with private subnets
  - Route internet traffic through a **NAT Gateway** (see below)

---

## 🌐 Step 2: Add NAT Gateway

### 2.1 Allocate Elastic IP

1. Go to **Elastic IPs → Allocate Elastic IP**
2. Create NAT Gateway in a **public subnet**
3. Attach the Elastic IP

### 2.2 Update Route Table

- Route `0.0.0.0/0` from **private route table** to the **NAT Gateway**

---

## 🖥️ Step 3: Configure EC2 and Launch Template

### 3.1 Create Launch Template

1. Go to **EC2 → Launch Templates → Create**
2. Set:
   - AMI: Amazon Linux 2
   - Instance Type: `t2.micro` (free tier eligible)
   - Key Pair: Optional for SSH access
   - Network Settings: Use default VPC or your custom one
3. Add **User Data** for bootstrapping:

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Launched via ASG</h1>" > /var/www/html/index.html
````

---

## 📈 Step 4: Create Auto Scaling Group (ASG)

### 4.1 Setup ASG

1. Go to **Auto Scaling Groups → Create**
2. Use your Launch Template
3. Select **private subnets** for instances
4. Attach it to a Target Group (created in next step)

### 4.2 Define Scaling Policies

* **Minimum Instances:** 1
* **Maximum Instances:** 3
* **Scaling Policy:** Target tracking (e.g., maintain average CPU ≤ 50%)

---

## ⚖️ Step 5: Configure Application Load Balancer (ALB)

### 5.1 Create Target Group

1. Go to **EC2 → Target Groups → Create**
2. Choose **Instances**, protocol **HTTP**, port **80**
3. Health check path: `/`

### 5.2 Create ALB

1. Go to **Load Balancers → Create**
2. Choose **Application Load Balancer**
3. Place in **public subnets**
4. Add **Listener** on port 80
5. Forward traffic to Target Group created above

---

## 🗂️ Step 6: Create Amazon S3 Bucket for Static Website

### 6.1 Create S3 Bucket

1. Go to **S3 → Create Bucket**
2. Name it uniquely (e.g., `my-app-static-site`)
3. Uncheck “Block all public access”
4. Enable **Static Website Hosting**

   * Index: `index.html`
   * Error: `error.html`

### 6.2 Set Bucket Policy

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": "*",
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::my-app-static-site/*"
  }]
}
```

---

## 🔄 Step 7: Configure CI/CD with GitHub Actions

### 7.1 Setup GitHub Repository

1. Push your static site (HTML, CSS, JS) to GitHub
2. Add a folder like `/public` containing website files

### 7.2 Add Secrets

In your GitHub repository:

* Go to **Settings → Secrets and Variables → Actions**
* Add:

  * `AWS_ACCESS_KEY_ID`
  * `AWS_SECRET_ACCESS_KEY`

> These credentials should belong to an IAM user with S3 write access.

---

### 7.3 Add GitHub Actions Workflow

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy to S3

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: your-region

      - name: Deploy to S3
        run: aws s3 sync ./public s3://my-app-static-site --delete
```

> Replace `./public` with your actual static files folder path.

---

## 🧪 Step 8: Test the Setup

1. Visit your **ALB DNS name** in the browser:

   * You should see the Apache web page or your deployed app.
2. Visit your **S3 website URL**:

   * Format: `http://<bucket-name>.s3-website-<region>.amazonaws.com`

---

## 📌 Optional Enhancements

* ✅ Use **Route 53** to attach custom domain
* ✅ Add **SSL (HTTPS)** using ACM + ALB
* ✅ Add **CloudFront CDN** in front of S3
* ✅ Use **Terraform or CloudFormation** for IaC automation

---

## 🧾 Summary

| Component      | Description                                |
| -------------- | ------------------------------------------ |
| VPC            | Isolated network with public/private zones |
| ALB            | Distributes traffic across EC2 instances   |
| EC2 + ASG      | Scalable compute environment               |
| S3 Bucket      | Static site hosting                        |
| GitHub Actions | Automates deployment to S3                 |

---

## ✅ You Did It!

You now have a **complete, automated AWS infrastructure** for deploying both dynamic (EC2) and static (S3) content, powered by GitHub CI/CD.

---
