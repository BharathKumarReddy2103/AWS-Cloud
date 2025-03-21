# Deploying a Real-Time Production-Grade Application on AWS: A VPC-Based Architecture with Load Balancer, Auto Scaling, and Bastion Host

## Introduction

Welcome to Day 7 of the **AWS Zero to Hero** series. In this guide, we will walk through building a **real-time, production-grade AWS project** using all the fundamental AWS concepts learned in the previous lessons—from **VPCs, subnets, and EC2 instances to load balancers, NAT Gateways, auto scaling groups, and Bastion hosts**.

This architecture mirrors a secure and scalable deployment model recommended by AWS for running production workloads on EC2 instances. We will break down the components, explain their purposes, and walk through the hands-on steps required to set everything up in your own AWS account.

---

## Project Overview

This project demonstrates how to:

- Design and implement a VPC with **public and private subnets** across **two availability zones** for high availability.
- Deploy a **web application** inside **private EC2 instances** using an **Auto Scaling Group**.
- Expose the application via an **Application Load Balancer (ALB)**.
- Secure EC2 access using a **Bastion host** in the public subnet.
- Ensure outbound internet connectivity for private EC2 instances via a **NAT Gateway**.

---

## Architecture Design

Here's the high-level flow of the architecture:

- **VPC** spans two Availability Zones (AZs).
- Each AZ contains a **public and private subnet**.
- **Public subnets** host the **NAT Gateway**, **Load Balancer**, and **Bastion Host**.
- **Private subnets** host the **application EC2 instances**, which do not have public IPs.
- Internet users access the application through the **ALB**.
- Outbound internet traffic from EC2s in private subnets is routed via the **NAT Gateway**.
  
![AWS Architecture Diagram](https://d1.awsstatic.com/diagrams/AWS_Public_Private_Subnet_Architecture.c1cd4067c5cdb2ad0558375e49e68f1dbd3db517.png)  
*Source: AWS Architecture Blog*

---

## Key Components Explained

### 1. VPC and Subnets

- A **Virtual Private Cloud (VPC)** is your isolated network within AWS.
- We use:
  - **2 Public Subnets** (1 per AZ) – for Load Balancer and Bastion.
  - **2 Private Subnets** (1 per AZ) – for application EC2s.
- Route tables:
  - Public subnets: route to **Internet Gateway (IGW)**.
  - Private subnets: route to **NAT Gateway**.

### 2. NAT Gateway

- Allows EC2 instances in private subnets to access the internet securely.
- It **masks the private IP** of EC2s with a **static Elastic IP**.
- Ideal for downloading packages or making API requests from private instances.

### 3. Application Load Balancer (ALB)

- An **L7 HTTP/HTTPS Load Balancer** that distributes traffic across EC2s.
- Supports **health checks**, **host/path-based routing**, and **SSL termination**.

### 4. Auto Scaling Group (ASG)

- Automatically launches and terminates EC2 instances based on:
  - **Desired count**
  - **Minimum/maximum capacity**
  - **Scaling policies** (e.g., CPU threshold)
- Ensures **high availability** and **cost optimization**.

### 5. Bastion Host (Jump Server)

- A publicly accessible EC2 instance in the **public subnet**.
- Acts as a gateway to **SSH into private EC2 instances**.
- Improves security by centralizing access and enabling **audit logging**.

---

## Step-by-Step Implementation

### Step 1: Create VPC and Subnets

- Go to VPC Dashboard → **Create VPC and more**
- Name: `aws-prod-vpc`
- Select:
  - 2 AZs (e.g., `us-east-1a`, `us-east-1b`)
  - 2 public subnets
  - 2 private subnets
  - Enable NAT Gateway (1 per AZ)
  - Skip S3 VPC endpoints for now

### Step 2: Launch Template for EC2

- Navigate to EC2 → Launch Templates
- Name: `aws-prod-template`
- AMI: Ubuntu 22.04
- Instance Type: `t2.micro`
- Security Group:
  - Allow **SSH (port 22)**
  - Allow **App port (e.g., 8000)**
- Key Pair: Use your `.pem` file for SSH access

### Step 3: Auto Scaling Group

- EC2 → Auto Scaling Groups → Create
- Select the launch template
- Choose private subnets
- Desired capacity: 2
- Skip load balancer (for now)

### Step 4: Bastion Host

- Launch EC2 instance manually
- Public subnet, with a public IP
- Security Group: Allow SSH
- SSH into Bastion, then use SCP to copy the `.pem` file
- From Bastion, SSH into private EC2 instances

```sh
# From local machine to Bastion
scp -i ~/.ssh/aws-key.pem aws-key.pem ubuntu@<bastion-ip>:~

# SSH into Bastion
ssh -i ~/.ssh/aws-key.pem ubuntu@<bastion-ip>

# From Bastion to Private EC2
ssh -i aws-key.pem ubuntu@<private-ip>
```

**Step 5: Deploy Sample App**

•	On private EC2, create a simple Python app:
echo "<html><h1>My First AWS App in Private Subnet</h1></html>" > index.html
python3 -m http.server 8000
•	Only deploy this on one EC2 to observe load balancing effects.
Step 6: Create Target Group
•	EC2 → Target Groups → Create
•	Type: EC2 instances
•	Port: 8000
•	Attach both EC2s (only one has the app running)
Step 7: Create Load Balancer
•	EC2 → Load Balancers → Create ALB
•	Name: aws-prod-alb
•	Scheme: Internet-facing
•	Select public subnets
•	Attach previously created security group (ensure port 80 is open)
•	Add listener on port 80, forward to target group
________________________________________
Testing and Verification
•	Wait until ALB is Active
•	Access ALB DNS via browser:
http://<load-balancer-dns>
•	You should see the app running.
•	Occasionally you may get a 5xx error — this means ALB hit the instance without the app (expected).
•	Deploy app on both instances to fully utilize load balancing.
________________________________________
Best Practices and Tips
•	Always use Bastion Hosts for accessing private EC2s securely.
•	Enable detailed monitoring on Auto Scaling for better scaling decisions.
•	Keep security groups restricted to necessary ports only.
•	Use launch templates for reusability and better management.
•	Use ALB health checks to route traffic to healthy instances only.
•	Clean up resources post-demo to avoid unnecessary AWS billing.
________________________________________
Conclusion
You’ve now built a production-grade AWS architecture from scratch using core AWS services. This real-time example showcases:
•	Best practices in network isolation
•	Use of NAT Gateway for secure internet access
•	Auto Scaling for resilience and elasticity
•	Load Balancing to ensure fault tolerance
•	SSH security via Bastion hosts
This setup is ideal for hosting microservices, internal applications, and any secure cloud-native deployments in AWS.
________________________________________
Further Reading
•	AWS VPC Documentation
•	Best Practices for Auto Scaling
•	Using a Bastion Host
•	AWS Elastic Load Balancing
________________________________________
Call for Contributions
If you found this guide helpful and want to contribute:
•	Submit a PR with additional enhancements
•	Suggest improvements or corrections in the issues tab
•	Fork and adapt this architecture for your use case
Let’s grow together as a community of DevOps Engineers
