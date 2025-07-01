# Deploying a Highly Available Java Application on AWS

This project demonstrates the deployment of a Java web application on AWS using the Rehosting Migration Strategy. The application is hosted on EC2 instances within an Auto Scaling Group to ensure high availability and scalability. An Application Load Balancer (ALB) distributes incoming traffic across multiple instances for optimal performance. AWS best practices for compute scalability, security, and cost optimization are implemented throughout the setup.
## 📌 Overview
![1740395679727](https://github.com/user-attachments/assets/87fcd004-41e6-4095-84c9-35b9b88ceb86)



---

## 🏗 Architecture Components

### 1️⃣ **Security Groups**
Security groups are configured to manage communication between instances and services.

✅ **Elastic Load Balancer Security Group**
The load balancer acts as a proxy for the application, accepting incoming traffic on port **80 (HTTP)**.
![2](https://github.com/user-attachments/assets/5a6b0649-d5a7-43ff-8a45-00f2d78516ef)


✅ **Tomcat Security Group**
The Tomcat server hosts the Java application and only accepts traffic from the Elastic Load Balancer.
![3](https://github.com/user-attachments/assets/40989ed1-b7fb-408c-938b-d6fde2bbdb45)


✅ **Backend Security Group**
This security group regulates traffic for MySQL, Memcached, and RabbitMQ instances.
![4](https://github.com/user-attachments/assets/4058f22e-86ad-4fcf-8a77-6b00c9848253)


---

### 2️⃣ **EC2 Instances**
![5](https://github.com/user-attachments/assets/019bafad-fdf4-4113-a485-2b884e749876)


✅ **MySQL EC2 Instance**
User data script to set up the instance:
```bash
#!/bin/bash
DATABASE_PASS='admin123'
sudo dnf update -y
sudo dnf install git zip unzip -y
sudo dnf install mariadb105-server -y
sudo systemctl start mariadb
sudo systemctl enable mariadb
cd /tmp/
git clone -b main https://github.com/hkhcoder/vprofile-project.git
sudo mysqladmin -u root password "$DATABASE_PASS"
sudo mysql -u root -p"$DATABASE_PASS" -e "create database accounts"
sudo mysql -u root -p"$DATABASE_PASS" accounts < /tmp/vprofile-project/src/main/resources/db_backup.sql
```

✅ **Memcached EC2 Instance**
```bash
#!/bin/bash
sudo dnf install memcached -y
sudo systemctl start memcached
sudo systemctl enable memcached
sed -i 's/127.0.0.1/0.0.0.0/g' /etc/sysconfig/memcached
sudo systemctl restart memcached
sudo memcached -p 11211 -U 11111 -u memcached -d
```

✅ **RabbitMQ EC2 Instance**
```bash
#!/bin/bash
rpm --import 'https://github.com/rabbitmq/signing-keys/releases/download/3.0/rabbitmq-release-signing-key.asc'
dnf install -y erlang rabbitmq-server
systemctl enable rabbitmq-server
systemctl start rabbitmq-server
sudo rabbitmqctl add_user test test
sudo rabbitmqctl set_user_tags test administrator
rabbitmqctl set_permissions -p / test ".*" ".*" ".*"
sudo systemctl restart rabbitmq-server
```

✅ **Tomcat Server EC2 Instance**
1. Install dependencies:
```bash
#!/bin/bash
sudo apt update
sudo apt install -y openjdk-17-jdk tomcat10 git
```
2. Copy WAR file from S3 and deploy on Tomcat.

---

### 3️⃣ **Route 53**
A private **Hosted Zone DNS** is set up for internal service communication.
![6](https://github.com/user-attachments/assets/299ce582-478d-4288-b787-87a704adac13)


---

### 4️⃣ **S3 Bucket**
An S3 bucket stores the Java application WAR file for deployment.
![7](https://github.com/user-attachments/assets/813d3d6c-29fd-473b-a23c-475090180626)


---

### 5️⃣ **IAM Roles and Users**

✅ **S3 Admin IAM User**
An IAM user with S3 access keys is created.
![8](https://github.com/user-attachments/assets/f88237f3-3be2-4b8f-86ee-440750daa282)


✅ **S3 Admin IAM Role**
A role is assigned to the EC2 instance to allow S3 access.
![9](https://github.com/user-attachments/assets/ba303cb5-1fba-4f95-8080-67c20e7c6be1)


---

### 6️⃣ **Elastic Load Balancer**

✅ **Target Group**
![10](https://github.com/user-attachments/assets/f42c8d8e-bc63-4a9c-8396-e0397db15277)


✅ **ELB Configuration**
![11](https://github.com/user-attachments/assets/f9d9f630-25e1-4a59-809b-3f9e1f23ffd4)


✅ **Listener Configuration**
![12](https://github.com/user-attachments/assets/4ce2ba71-c228-4e30-84ea-f6d97d1e7c7d)


---

### 7️⃣ **Auto Scaling**

✅ **Launch Template**
1. **AMI Creation for Tomcat Server**
![13](https://github.com/user-attachments/assets/38ddc145-e9c2-4e3e-ac07-1c8d345c462e)

2. **Launch Template Configuration**
![14](https://github.com/user-attachments/assets/85bfa8e9-b6b6-4b39-88f2-0c9493cd293c)


✅ **Auto Scaling Group Configuration**
![14](https://github.com/user-attachments/assets/e617da03-1b68-42c7-9aa9-4205073e682a)


🎉 **Application is Live!**
![16](https://github.com/user-attachments/assets/2c8d6401-9290-4d8f-93e3-f55494d3f566)




---
