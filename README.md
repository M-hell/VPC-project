# Virtual Private Cloud using AWS Cloud Infrastructure with Automated CI/CD Pipeline

![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/github%20actions-%232671E5.svg?style=for-the-badge&logo=githubactions&logoColor=white)
![EC2](https://img.shields.io/badge/Amazon%20EC2-FF9900?style=for-the-badge&logo=amazon-ec2&logoColor=white)
![S3](https://img.shields.io/badge/Amazon%20S3-569A31?style=for-the-badge&logo=amazon-s3&logoColor=white)
![VPC](https://img.shields.io/badge/Amazon%20VPC-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)

## 🚀 Project Overview

This project demonstrates a comprehensive, production-grade AWS cloud infrastructure that combines scalable compute resources with automated deployment capabilities. The architecture showcases modern cloud engineering practices by implementing a robust, multi-tier application hosting environment alongside a fully automated CI/CD pipeline.

### 🎯 Project Goals

- **Scalable Infrastructure**: Build a highly available, auto-scaling web application hosting environment
- **Network Security**: Implement proper network isolation using VPC with public and private subnets
- **Load Distribution**: Ensure optimal traffic distribution and high availability through Application Load Balancer
- **Automated Deployment**: Achieve zero-downtime deployments with GitHub Actions CI/CD pipeline
- **Static Content Delivery**: Efficiently serve static websites through Amazon S3 hosting
- **Infrastructure as Code**: Demonstrate best practices for cloud resource management

### 🏆 Key Benefits

- **High Availability**: Multi-AZ deployment with automatic failover capabilities
- **Cost Optimization**: Auto Scaling Groups adjust capacity based on demand
- **Security**: Network-level isolation and proper security group configurations
- **Automation**: Fully automated deployment pipeline reduces manual intervention
- **Monitoring**: Built-in health checks and monitoring across all components
- **Scalability**: Horizontal scaling capabilities for handling variable workloads

---

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                          AWS Cloud                              │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                Custom VPC                               │    │
│  │                                                         │    │
│  │  ┌─────────────────┐    ┌─────────────────┐             │    │
│  │  │  Public Subnet  │    │  Private Subnet │             │    │
│  │  │                 │    │                 │             │    │
│  │  │  ┌───────────┐  │    │  ┌───────────┐  │             │    │
│  │  │  │    ALB    │  │    │  │    EC2    │  │             │    │
│  │  │  └───────────┘  │    │  │ Instances │  │             │    │
│  │  │                 │    │  └───────────┘  │             │    │
│  │  └─────────────────┘    └─────────────────┘             │    │
│  │                                                         │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                   Amazon S3                             │    │
│  │              Static Website Hosting                     │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                                │
                                │ GitHub Actions
                                │ CI/CD Pipeline
                                │
                    ┌─────────────────────┐
                    │   GitHub Repository │
                    └─────────────────────┘
```

> **Note**: Detailed architecture diagrams and flowcharts will be added in future updates to provide visual representation of traffic flow, VPC structure, and CI/CD processes.

---

## 🛠️ AWS Services Architecture

### 🌐 Amazon VPC (Virtual Private Cloud)

**Amazon VPC** is a logically isolated network environment within the AWS cloud that provides complete control over your virtual networking environment. It acts as your private data center in the cloud.

**Key Components:**

- **Custom VPC**: Creates an isolated network space with customizable IP address ranges (CIDR blocks)
- **Public Subnets**: Network segments with direct internet access through Internet Gateway
  - Houses the Application Load Balancer for external traffic handling
  - Configured with route tables directing traffic to Internet Gateway
- **Private Subnets**: Secure network segments without direct internet access
  - Hosts EC2 instances for enhanced security
  - Outbound internet access via NAT Gateway for updates and external API calls
- **Internet Gateway (IGW)**: Enables bidirectional internet connectivity for public subnets
- **NAT Gateway**: Provides secure outbound internet access for private subnet resources
- **Route Tables**: Define traffic routing rules between subnets and gateways
- **Security Groups**: Act as virtual firewalls controlling inbound/outbound traffic at instance level

**Benefits**: Network isolation, security through depth, compliance readiness, and complete control over network configuration.

### 🖥️ Amazon EC2 (Elastic Compute Cloud)

**Amazon EC2** provides resizable compute capacity in the cloud, allowing you to launch virtual servers (instances) with various configurations based on your application requirements.

**Implementation Details:**

- **Instance Types**: Optimized for specific workloads (compute-optimized, memory-optimized, general-purpose)
- **Launch Templates**: Predefined configurations including AMI ID, instance type, security groups, and user data
- **User Data Scripts**: Automated bootstrapping scripts that run during instance initialization
  - Install application dependencies
  - Configure web servers (Apache, Nginx)
  - Download application code
  - Set up monitoring agents
- **Security Groups**: Instance-level firewall rules controlling network access
- **Key Pairs**: Secure SSH access mechanism for instance management
- **Placement Groups**: Control instance placement for optimal performance

**Benefits**: Scalable compute resources, pay-per-use pricing, wide variety of instance types, and integration with other AWS services.

### 📈 Auto Scaling Groups (ASG)

**Auto Scaling Groups** automatically adjust the number of EC2 instances in response to demand, ensuring application availability while optimizing costs.

**Configuration Components:**

- **Launch Template Integration**: Uses predefined templates to launch consistent instances
- **Scaling Policies**: Define when and how to scale resources
  - **Target Tracking**: Maintains specific metric values (CPU utilization, request count)
  - **Step Scaling**: Scales in steps based on CloudWatch alarms
  - **Scheduled Scaling**: Predictive scaling based on known patterns
- **Health Checks**: Monitors instance health using:
  - EC2 system status checks
  - ELB health checks
  - Custom health checks
- **Multi-AZ Deployment**: Distributes instances across multiple Availability Zones
- **Cooldown Periods**: Prevents rapid scaling actions that could cause instability

**Benefits**: High availability, cost optimization, automatic failure recovery, and seamless capacity management.

### ⚖️ Application Load Balancer (ALB) & Target Groups

**Application Load Balancer** operates at the application layer (Layer 7) and intelligently distributes incoming application traffic across multiple targets.

**ALB Features:**

- **Layer 7 Routing**: Content-based routing using HTTP/HTTPS headers, paths, and query strings
- **SSL/TLS Termination**: Handles encryption/decryption, reducing compute load on backend instances
- **WebSocket Support**: Enables real-time communication applications
- **HTTP/2 Support**: Improved performance for modern web applications
- **Cross-Zone Load Balancing**: Distributes traffic evenly across all registered targets in all enabled AZs

**Target Groups Configuration:**

- **Health Checks**: Regularly tests target health using configurable parameters
  - Health check path and protocol
  - Success codes (200, 201, etc.)
  - Timeout and interval settings
  - Healthy/unhealthy threshold counts
- **Sticky Sessions**: Routes requests from same client to same target
- **Request Routing**: Distributes requests based on configured algorithms
- **Target Registration**: Automatic registration/deregistration with Auto Scaling Groups

**Benefits**: High availability, intelligent traffic distribution, SSL offloading, and detailed monitoring capabilities.

### 🗂️ Amazon S3 Static Website Hosting

**Amazon S3** provides object storage with web hosting capabilities, perfect for serving static websites with high durability and availability.

**Static Website Configuration:**

- **Bucket Policy**: JSON-based access control policy enabling public read access
- **Static Website Hosting**: Enables web server functionality with index and error documents
- **Public Access Settings**: Carefully configured to allow public website access while maintaining security
- **Hosting Endpoint**: Provides a unique URL for accessing your static website
- **Error Handling**: Custom error pages for 404 and other HTTP errors
- **Logging**: Access logs for monitoring and analytics

**Security Considerations:**

- **Bucket-level Permissions**: Restrict access to specific actions (GetObject only for public access)
- **CORS Configuration**: Control cross-origin requests for web applications
- **Versioning**: Maintain multiple versions of objects for rollback capabilities
- **Encryption**: Server-side encryption for data at rest

**Benefits**: Cost-effective hosting, global edge caching via CloudFront integration, 99.999999999% durability, and seamless scaling.

### 🔄 CI/CD Pipeline with GitHub Actions

**GitHub Actions** provides a powerful platform for automating software development workflows, enabling continuous integration and deployment directly from your repository.

**Workflow Components:**

- **Triggers**: Automated execution based on:
  - Push events to specific branches
  - Pull request creation/updates
  - Scheduled intervals (cron jobs)
  - Manual workflow dispatch
- **Actions**: Reusable units of code that perform specific tasks
  - `aws-actions/configure-aws-credentials`: Secure AWS authentication
  - `actions/checkout`: Repository code checkout
  - Custom deployment scripts
- **Secrets Management**: Secure storage of sensitive information
  - AWS Access Keys
  - AWS Secret Access Keys
  - AWS Session Tokens
- **Environment Variables**: Configuration values for different deployment stages

**Deployment Process:**

1. **Code Checkout**: Downloads repository content to runner
2. **AWS Authentication**: Configures AWS credentials using OIDC or access keys
3. **Build Process**: Compiles, tests, and packages application
4. **S3 Synchronization**: Uses `aws s3 sync` to update static website content
5. **Cache Invalidation**: Clears CloudFront cache if configured
6. **Deployment Verification**: Confirms successful deployment

**Benefits**: Automated deployment, version control integration, audit trails, and scalable CI/CD processes.

---

## 📋 Prerequisites

Before deploying this infrastructure, ensure you have:

### AWS Account Setup
- [ ] Active AWS account with appropriate permissions
- [ ] AWS CLI installed and configured
- [ ] AWS Access Keys with the following permissions:
  - EC2 Full Access
  - VPC Full Access
  - S3 Full Access
  - IAM permissions for creating roles and policies
  - Application Load Balancer permissions

### Development Environment
- [ ] Git installed locally
- [ ] GitHub account with repository access
- [ ] Basic understanding of AWS services
- [ ] Text editor or IDE for configuration files

### Required AWS Permissions
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:*",
        "elasticloadbalancing:*",
        "autoscaling:*",
        "s3:*",
        "iam:PassRole",
        "iam:CreateRole",
        "iam:AttachRolePolicy"
      ],
      "Resource": "*"
    }
  ]
}
```

---

## 🚀 How to Deploy

### Step 1: VPC Infrastructure Setup

#### 1.1 Create Custom VPC
```bash
# Create VPC
aws ec2 create-vpc \
  --cidr-block 10.0.0.0/16 \
  --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=MyAppVPC}]'

# Enable DNS hostnames
aws ec2 modify-vpc-attribute \
  --vpc-id vpc-xxxxxxxxx \
  --enable-dns-hostnames
```

#### 1.2 Create Subnets
```bash
# Create Public Subnet
aws ec2 create-subnet \
  --vpc-id vpc-xxxxxxxxx \
  --cidr-block 10.0.1.0/24 \
  --availability-zone us-east-1a \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=PublicSubnet1}]'

# Create Private Subnet
aws ec2 create-subnet \
  --vpc-id vpc-xxxxxxxxx \
  --cidr-block 10.0.2.0/24 \
  --availability-zone us-east-1b \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=PrivateSubnet1}]'
```

#### 1.3 Configure Internet Gateway
```bash
# Create Internet Gateway
aws ec2 create-internet-gateway \
  --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=MyAppIGW}]'

# Attach to VPC
aws ec2 attach-internet-gateway \
  --internet-gateway-id igw-xxxxxxxxx \
  --vpc-id vpc-xxxxxxxxx
```

#### 1.4 Setup Route Tables
```bash
# Create public route table
aws ec2 create-route-table \
  --vpc-id vpc-xxxxxxxxx \
  --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=PublicRouteTable}]'

# Add route to internet gateway
aws ec2 create-route \
  --route-table-id rtb-xxxxxxxxx \
  --destination-cidr-block 0.0.0.0/0 \
  --gateway-id igw-xxxxxxxxx

# Associate with public subnet
aws ec2 associate-route-table \
  --route-table-id rtb-xxxxxxxxx \
  --subnet-id subnet-xxxxxxxxx
```

### Step 2: Security Groups Configuration

#### 2.1 ALB Security Group
```bash
# Create ALB Security Group
aws ec2 create-security-group \
  --group-name ALB-SecurityGroup \
  --description "Security group for Application Load Balancer" \
  --vpc-id vpc-xxxxxxxxx

# Allow HTTP traffic
aws ec2 authorize-security-group-ingress \
  --group-id sg-xxxxxxxxx \
  --protocol tcp \
  --port 80 \
  --cidr 0.0.0.0/0

# Allow HTTPS traffic
aws ec2 authorize-security-group-ingress \
  --group-id sg-xxxxxxxxx \
  --protocol tcp \
  --port 443 \
  --cidr 0.0.0.0/0
```

#### 2.2 EC2 Security Group
```bash
# Create EC2 Security Group
aws ec2 create-security-group \
  --group-name EC2-SecurityGroup \
  --description "Security group for EC2 instances" \
  --vpc-id vpc-xxxxxxxxx

# Allow traffic from ALB
aws ec2 authorize-security-group-ingress \
  --group-id sg-yyyyyyyyy \
  --protocol tcp \
  --port 80 \
  --source-group sg-xxxxxxxxx
```

### Step 3: Launch Template Creation

#### 3.1 Create Launch Template
```bash
aws ec2 create-launch-template \
  --launch-template-name MyAppLaunchTemplate \
  --launch-template-data '{
    "ImageId": "ami-0abcdef1234567890",
    "InstanceType": "t3.micro",
    "SecurityGroupIds": ["sg-yyyyyyyyy"],
    "UserData": "IyEvYmluL2Jhc2gKYXB0IHVwZGF0ZSAteQphcHQgaW5zdGFsbCAteSBhcGFjaGUyCnN5c3RlbWN0bCBzdGFydCBhcGFjaGUyCnN5c3RlbWN0bCBlbmFibGUgYXBhY2hlMg==",
    "TagSpecifications": [
      {
        "ResourceType": "instance",
        "Tags": [
          {
            "Key": "Name",
            "Value": "MyAppInstance"
          }
        ]
      }
    ]
  }'
```

### Step 4: Application Load Balancer Setup

#### 4.1 Create Application Load Balancer
```bash
# Create ALB
aws elbv2 create-load-balancer \
  --name MyAppALB \
  --subnets subnet-xxxxxxxxx subnet-yyyyyyyyy \
  --security-groups sg-xxxxxxxxx \
  --scheme internet-facing \
  --type application \
  --ip-address-type ipv4
```

#### 4.2 Create Target Group
```bash
# Create Target Group
aws elbv2 create-target-group \
  --name MyAppTargetGroup \
  --protocol HTTP \
  --port 80 \
  --vpc-id vpc-xxxxxxxxx \
  --health-check-path /health \
  --health-check-interval-seconds 30 \
  --health-check-timeout-seconds 5 \
  --healthy-threshold-count 2 \
  --unhealthy-threshold-count 3
```

#### 4.3 Create Listener
```bash
# Create Listener
aws elbv2 create-listener \
  --load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:123456789012:loadbalancer/app/MyAppALB/1234567890123456 \
  --protocol HTTP \
  --port 80 \
  --default-actions Type=forward,TargetGroupArn=arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/MyAppTargetGroup/1234567890123456
```

### Step 5: Auto Scaling Group Configuration

#### 5.1 Create Auto Scaling Group
```bash
aws autoscaling create-auto-scaling-group \
  --auto-scaling-group-name MyAppASG \
  --launch-template LaunchTemplateName=MyAppLaunchTemplate,Version=1 \
  --min-size 2 \
  --max-size 6 \
  --desired-capacity 2 \
  --vpc-zone-identifier "subnet-xxxxxxxxx,subnet-yyyyyyyyy" \
  --target-group-arns arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/MyAppTargetGroup/1234567890123456 \
  --health-check-type ELB \
  --health-check-grace-period 300
```

#### 5.2 Configure Scaling Policies
```bash
# Scale Out Policy
aws autoscaling put-scaling-policy \
  --auto-scaling-group-name MyAppASG \
  --policy-name ScaleOutPolicy \
  --policy-type TargetTrackingScaling \
  --target-tracking-configuration '{
    "TargetValue": 70.0,
    "PredefinedMetricSpecification": {
      "PredefinedMetricType": "ASGAverageCPUUtilization"
    }
  }'
```

### Step 6: S3 Static Website Setup

#### 6.1 Create S3 Bucket
```bash
# Create S3 bucket
aws s3 mb s3://my-app-static-website-bucket-unique-name

# Enable static website hosting
aws s3 website s3://my-app-static-website-bucket-unique-name \
  --index-document index.html \
  --error-document error.html
```

#### 6.2 Configure Bucket Policy
```bash
# Create bucket policy file
cat > bucket-policy.json << EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-app-static-website-bucket-unique-name/*"
    }
  ]
}
EOF

# Apply bucket policy
aws s3api put-bucket-policy \
  --bucket my-app-static-website-bucket-unique-name \
  --policy file://bucket-policy.json
```

### Step 7: GitHub Actions CI/CD Setup

#### 7.1 Configure GitHub Secrets
Navigate to your GitHub repository → Settings → Secrets and Variables → Actions

Add the following secrets:
- `AWS_ACCESS_KEY_ID`: Your AWS access key
- `AWS_SECRET_ACCESS_KEY`: Your AWS secret key
- `AWS_REGION`: Your AWS region (e.g., us-east-1)
- `S3_BUCKET_NAME`: Your S3 bucket name

#### 7.2 Create GitHub Actions Workflow
Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy to AWS S3

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v4
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: ${{ secrets.AWS_REGION }}
        
    - name: Deploy to S3
      run: |
        aws s3 sync ./src s3://${{ secrets.S3_BUCKET_NAME }} --delete
        
    - name: Invalidate CloudFront (if configured)
      run: |
        aws cloudfront create-invalidation \
          --distribution-id ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }} \
          --paths "/*"
```

---

## 🔄 CI/CD Pipeline Deep Dive

### Pipeline Architecture

The CI/CD pipeline creates a seamless integration between your GitHub repository and AWS infrastructure, enabling automated deployments with every code change.

### Workflow Triggers

**Push Events**: Automatically triggered when code is pushed to the main branch
- Validates code changes
- Runs automated tests (if configured)
- Deploys to production environment

**Pull Request Events**: Triggered on PR creation/updates
- Enables preview deployments
- Runs validation checks
- Provides deployment status feedback

**Manual Dispatch**: Allows manual workflow execution
- Useful for hotfixes
- Emergency deployments
- Testing pipeline changes

### GitHub Actions Workflow Steps

#### 1. Environment Setup
```yaml
- name: Setup Node.js
  uses: actions/setup-node@v4
  with:
    node-version: '18'
    cache: 'npm'
```

#### 2. Dependency Installation
```yaml
- name: Install dependencies
  run: npm ci
```

#### 3. Build Process
```yaml
- name: Build application
  run: npm run build
```

#### 4. Testing
```yaml
- name: Run tests
  run: npm test
```

#### 5. AWS Authentication
```yaml
- name: Configure AWS credentials
  uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
    aws-region: ${{ secrets.AWS_REGION }}
```

#### 6. Deployment
```yaml
- name: Deploy to S3
  run: |
    aws s3 sync ./dist s3://${{ secrets.S3_BUCKET_NAME }} \
      --delete \
      --cache-control max-age=31536000 \
      --exclude "*.html" \
      --exclude "service-worker.js"
    
    aws s3 sync ./dist s3://${{ secrets.S3_BUCKET_NAME }} \
      --delete \
      --cache-control max-age=0 \
      --include "*.html" \
      --include "service-worker.js"
```

### Security Best Practices

**AWS Credentials Management**:
- Use IAM roles with minimal required permissions
- Implement OIDC for secure authentication
- Rotate credentials regularly
- Never commit credentials to repository

**Secrets Management**:
- Store sensitive information in GitHub Secrets
- Use environment-specific secret scoping
- Implement secret rotation policies
- Monitor secret usage and access

### Monitoring and Logging

**Deployment Monitoring**:
- CloudWatch integration for deployment metrics
- GitHub Actions logs for debugging
- AWS CLI output for operation confirmation
- Custom notification systems (Slack, email)

**Error Handling**:
- Retry mechanisms for transient failures
- Rollback procedures for failed deployments
- Comprehensive error logging
- Alert systems for critical failures

---

## 📊 Monitoring and Maintenance

### Health Checks and Monitoring

**ALB Health Checks**:
- Monitor target health status
- Configure appropriate health check paths
- Set optimal timeout and interval values
- Enable detailed health check logging

**Auto Scaling Monitoring**:
- Track scaling activities and policies
- Monitor CloudWatch metrics (CPU, memory, network)
- Set up alarms for scaling events
- Review scaling history and patterns

**S3 Monitoring**:
- Monitor bucket access patterns
- Track data transfer and storage costs
- Set up CloudWatch alarms for unusual activity
- Enable access logging for security auditing

### Cost Optimization

**EC2 Cost Management**:
- Use appropriate instance types for workload
- Implement scheduled scaling for predictable patterns
- Consider Reserved Instances for baseline capacity
- Monitor and optimize data transfer costs

**S3 Cost Optimization**:
- Implement lifecycle policies for old content
- Use appropriate storage classes
- Enable compression for text files
- Monitor data transfer costs

### Security Maintenance

**Regular Security Updates**:
- Keep AMIs updated with latest security patches
- Review and update security group rules
- Implement AWS Config for compliance monitoring
- Regular security audits and penetration testing

**Access Control**:
- Implement least privilege access principles
- Regular review of IAM policies and roles
- Enable CloudTrail for API activity logging
- Monitor and alert on suspicious activities

---

## 🎯 Live Demo

> **Demo URL**: *[URL will be added after deployment]*

### Demo Features
- **Responsive Design**: Optimized for desktop and mobile devices
- **Load Testing**: Demonstrates auto-scaling capabilities
- **Real-time Monitoring**: Live metrics and performance data
- **CI/CD Integration**: Shows automated deployment process

### Demo Scenarios
1. **Traffic Spike Simulation**: Demonstrates auto-scaling in action
2. **Deployment Pipeline**: Shows automated deployment from GitHub
3. **Health Check Monitoring**: Displays ALB health check functionality
4. **Cross-AZ Failover**: Demonstrates high availability features

---

## 📈 Performance Metrics

### Expected Performance Benchmarks

**Application Load Balancer**:
- Response time: < 100ms for local requests
- Throughput: Up to 1,000 requests per second per instance
- Availability: 99.99% uptime SLA

**Auto Scaling Response**:
- Scale-out time: 2-3 minutes for new instances
- Scale-in time: 5-10 minutes (configurable cooldown)
- Health check response: < 30 seconds

**S3 Static Website**:
- First byte time: < 100ms globally
- Transfer rate: > 1 GB/s aggregate
- Availability: 99.999999999% (11 9's) durability

---

## 🔧 Troubleshooting Guide

### Common Issues and Solutions

**EC2 Instances Not Launching**:
- Check launch template configuration
- Verify security group rules
- Ensure sufficient subnet IP addresses
- Review IAM instance profile permissions

**ALB Health Check Failures**:
- Verify health check path exists
- Check security group rules for health check traffic
- Ensure application is properly listening on configured port
- Review health check timeout and interval settings

**S3 Deployment Failures**:
- Verify bucket permissions and policies
- Check AWS credentials and permissions
- Ensure bucket name is unique and valid
- Review GitHub Actions logs for specific errors

**Auto Scaling Not Working**:
- Check CloudWatch metrics and alarms
- Verify scaling policies configuration
- Ensure proper IAM permissions for scaling
- Review cooldown periods and scaling history

### Debugging Commands

```bash
# Check ALB target health
aws elbv2 describe-target-health \
  --target-group-arn arn:aws:elasticloadbalancing:region:account:targetgroup/name/id

# View Auto Scaling Group activity
aws autoscaling describe-scaling-activities \
  --auto-scaling-group-name MyAppASG

# Check EC2 instance status
aws ec2 describe-instance-status \
  --instance-ids i-1234567890abcdef0

# View S3 bucket policy
aws s3api get-bucket-policy \
  --bucket my-app-static-website-bucket-unique-name
```

---

## 🤝 Contributing

We welcome contributions to improve this project! Please follow these guidelines:

### How to Contribute

1. **Fork the Repository**
   ```bash
   git clone https://github.com/yourusername/aws-infrastructure-project.git
   cd aws-infrastructure-project
   ```

2. **Create Feature Branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

3. **Make Changes**
   - Update documentation
   - Improve infrastructure code
   - Add new features
   - Fix bugs

4. **Test Changes**
   - Test infrastructure changes in development environment
   - Validate documentation updates
   - Run any existing tests

5. **Submit Pull Request**
   - Provide clear description of changes
   - Include any necessary documentation updates
   - Reference related issues

### Contribution Guidelines

- Follow AWS best practices and security guidelines
- Update documentation for any infrastructure changes
- Include clear commit messages
- Test changes thoroughly before submitting
- Maintain backward compatibility when possible

---

## 🛡️ Security Considerations

### Network Security
- VPC provides network isolation
- Security groups act as virtual firewalls
- Private subnets protect backend instances
- NAT Gateway provides secure outbound access

### Access Control
- IAM roles and policies for fine-grained permissions
- Least privilege principle implementation
- Regular access reviews and audits
- MFA enforcement for sensitive operations

### Data Protection
- S3 bucket encryption at rest
- SSL/TLS encryption in transit
- Regular security updates and patches
- Compliance with industry standards

---

## 📚 Additional Resources

### AWS Documentation
- [VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)
- [Application Load Balancer Guide](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/)
- [Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/ec2/userguide/)
- [S3 Static Website Hosting](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html)

### Best Practices
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [AWS Security Best Practices](https://aws.amazon.com/security/best-practices/)
- [Cost Optimization Best Practices](https://aws.amazon.com/pricing/cost-optimization/)

### Community Resources
- [AWS Community Forums](https://forums.aws.amazon.com/)
- [AWS re:Invent Sessions](https://reinvent.awsevents.com/)
- [AWS Samples GitHub Repository](https://github.com/aws-samples)

---

## 📄 License

This project is licensed under the MIT License -