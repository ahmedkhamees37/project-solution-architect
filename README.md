# 🚀 Scalable Web Application with ALB & Auto Scaling

Deploy a secure, scalable, and highly available web application on AWS using EC2 instances behind an Application Load Balancer (ALB) with Auto Scaling Groups (ASG).

---

## 📝 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Features](#features)
- [AWS Services Used](#aws-services-used)
- [Deployment](#deployment)
  - [Prerequisites](#prerequisites)
  - [Step-by-Step Guide](#step-by-step-guide)
- [Cost Optimization](#cost-optimization)
- [Cleanup](#cleanup)
- [License](#license)

---

## 🔷 Overview

This solution deploys a simple web application (Nginx) on AWS EC2 instances with high availability and automatic scaling using ALB and ASG. It demonstrates best practices for scalability, security, and cost optimization.

---

## 🏗️ Architecture

![Architecture Diagram](link-to-your-diagram.png)

### **Flow Explanation**

1. User requests are routed through the **Application Load Balancer (ALB)**.
2. ALB distributes traffic across EC2 instances in the **Auto Scaling Group**.
3. The ASG spans **multiple Availability Zones** for high availability.
4. **CloudWatch** monitors instance health and performance.
5. **SNS** sends alerts for scaling activities or alarms.

---

## ✨ Features

- Highly available web app using **multiple AZs**.
- **Auto Scaling policies** to handle demand changes.
- **IAM roles** for secure access.
- **CloudWatch alarms** with **SNS notifications**.
- Lightweight **user-data bootstrap** for Nginx installation.

---

## 🛠️ AWS Services Used

- Amazon EC2
- Auto Scaling Group (ASG)
- Application Load Balancer (ALB)
- IAM
- CloudWatch
- SNS

---

## 🚀 Deployment

### ✅ Prerequisites

- AWS Account
- IAM user with sufficient permissions (EC2, ALB, ASG, IAM, SNS, CloudWatch)
- AWS CLI configured or Management Console access
- SSH key pair (e.g., `Ahmed.pem`)

---

### ✅ Step-by-Step Guide

1. **Create an IAM Role for EC2**
   - Service: EC2
   - Policies:
     - `AmazonEC2ReadOnlyAccess`
     - `CloudWatchAgentServerPolicy`

2. **Prepare User Data Script**

    ```bash
    #!/bin/bash
    yum update -y
    yum install -y nginx
    echo "<h1>Hello from $(hostname -f)</h1>" > /usr/share/nginx/html/index.html
    systemctl enable nginx
    systemctl start nginx
    ```

3. **Create Launch Template**
   - AMI: Amazon Linux 2
   - Instance Type: t2.micro
   - Key Pair: Ahmed.pem
   - Security Group: Allow **HTTP (80)** and **SSH (22)** from your IP
   - Attach IAM role
   - Paste user-data script

4. **Create Auto Scaling Group**
   - Launch template: select above
   - VPC: choose default/custom
   - Subnets: select **2+ AZs**
   - Desired capacity: 2  
   - Minimum: 1  
   - Maximum: 3  
   - Scaling policy: Target tracking (CPU > 50% scale out, < 20% scale in)

5. **Create Application Load Balancer**
   - Type: Internet-facing
   - Listeners: HTTP (80)
   - Target Group: EC2 Instances
   - Health Check: `/`

6. **Configure CloudWatch & SNS**
   - **CloudWatch Alarm**
     - Metric: EC2 CPU Utilization
     - Threshold: >70% for 5 minutes
   - **SNS Topic**
     - Subscribe your email
     - Link to CloudWatch alarm action

7. **Test**
   - Access ALB DNS from browser
   - Refresh to see load balancing across instances

---

## 💡 Cost Optimization

- Use **t3.micro / t3a.micro** instances.
- Set minimum ASG capacity to **1** during low load.
- Clean up unused resources after testing.

---

## 🧹 Cleanup

To avoid unwanted AWS charges:

- Delete ALB
- Delete Auto Scaling Group
- Delete Launch Template
- Delete IAM Role
- Delete SNS Topic and CloudWatch Alarms

---

## 📄 License

This project is licensed under the MIT License.

---

## 🔗 References

- [AWS EC2 Auto Scaling Documentation](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)
- [Application Load Balancer Guide](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)

---

---

