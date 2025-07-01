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
- [License](#license)

---

## 🔷 Overview

This solution deploys a simple web application (Nginx) on AWS EC2 instances with high availability and automatic scaling using ALB and ASG. It demonstrates best practices for scalability, security, and cost optimization.

---

## 🏗️ Architecture

![1742896656417](https://github.com/user-attachments/assets/ba73f26a-bcd7-4218-8e61-acc0918d3562)



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
  ![Screenshot 2025-07-01 093652](https://github.com/user-attachments/assets/d672df8e-880a-4faa-976e-f765e86cc21e)


2. **Prepare User Data Script**

    ```bash
    #!/bin/bash
    yum update -y
    yum install -y nginx
    echo "<h1>Hello from $(hostname -f)</h1>" > /usr/share/nginx/html/index.html
    systemctl enable nginx
    systemctl start nginx
    ```

3. **Create ec2**
  ![Screenshot 2025-07-01 093807](https://github.com/user-attachments/assets/769e1004-04c3-4f20-91b5-a6a992bad952)


4. **Create Auto Scaling Group**
  ![Screenshot 2025-07-01 093514](https://github.com/user-attachments/assets/8d2e6d0b-9d91-4df9-b8cb-ec12c9be00e1)
![Screenshot 2025-07-01 093523](https://github.com/user-attachments/assets/087ffb8e-5f16-4eac-a78c-843b1d80b4e5)
![Screenshot 2025-07-01 093536](https://github.com/user-attachments/assets/197dc819-721d-4cc6-9245-6f5b7c87a266)
![Screenshot 2025-07-01 093546](https://github.com/user-attachments/assets/2ec63d9c-df35-46e3-8cdf-b57808edf027)
![Screenshot 2025-07-01 093818](https://github.com/user-attachments/assets/b8da32fd-c2b4-4129-aa42-2f7f8cebea50)




6. **Create Application Load Balancer**
   - Type: Internet-facing
   - Listeners: HTTP (80)
   - Target Group: EC2 Instances
   - Health Check: `/`
  ![Screenshot 2025-07-01 093830](https://github.com/user-attachments/assets/399139fc-ed71-4b8e-8c6c-fad352533047)


7. **Configure CloudWatch & SNS**
   - **CloudWatch Alarm**
     - Metric: EC2 CPU Utilization
     - Threshold: >70% for 5 minutes
   - **SNS Topic**
     - Subscribe your email
     - Link to CloudWatch alarm action

8. **Test**
   - Access ALB DNS from browser
   - Refresh to see load balancing across instances

![Screenshot 2025-07-01 093840](https://github.com/user-attachments/assets/f1c898d2-47a3-440a-9dac-034291706f7b)
![Screenshot 2025-07-01 093850](https://github.com/user-attachments/assets/5d8c54ca-7e81-4d55-bba8-92c679a2e753)


---




## 📄 License

This project is licensed under the MIT License.

---

## 🔗 References

- [AWS EC2 Auto Scaling Documentation](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)
- [Application Load Balancer Guide](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)

---

---

