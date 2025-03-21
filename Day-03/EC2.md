**AWS EC2: Deploy Your First Application on the Cloud**

Welcome to this detailed guide from the **AWS Zero to Hero Series**, where we’ll dive deep into one of the most essential services in AWS — **Amazon EC2 (Elastic Compute Cloud)**. This article is perfect for DevOps engineers, cloud practitioners, and anyone beginning their cloud journey with AWS.

By the end of this guide, you'll understand:

•	What EC2 is and why it’s widely used

•	Types of EC2 instances

•	AWS regions and availability zones

•	How to launch your first EC2 instance

•	Deploying a real-world application (Jenkins) on your EC2 instance

•	Accessing your application securely from the internet

---

**What Is Amazon EC2?**

**EC2** stands for **Elastic Compute Cloud**. It is AWS's virtual server offering that provides scalable computing capacity in the cloud. It allows you to run applications on virtual machines without investing in physical hardware.

**Breakdown of the Term:**

•	**Elastic**: You can scale the resources (CPU, RAM, storage) up or down based on your needs.

•	**Compute**: The CPU and memory resources required for your application.

•	**Cloud**: These virtual machines are hosted in AWS’s global data centers and accessed over the internet.

**Why EC2?**

Using EC2 eliminates the burden of maintaining physical servers, ensuring:

•	Reduced operational overhead (AWS handles infrastructure and hardware maintenance)

•	Cost efficiency (pay-as-you-go pricing model)

•	High availability and scalability

---

**Why Use EC2 Instead of On-Prem Servers?**

Imagine maintaining thousands of servers manually—installing updates, monitoring uptime, handling failures. AWS EC2 offloads this burden and offers:

•	**Auto scalability**

•	**Managed infrastructure**

•	**Built-in monitoring and security**

•	**Pay-for-what-you-use model**

•	**Global availability**

Additionally, EC2 allows you to shut down resources during off-peak hours to save costs, something not possible with on-prem servers that are always-on.

---

**EC2 Instance Types Explained**

AWS offers different EC2 instance families optimized for specific workloads:

| Instance Type           | Use Case                                                      |
|-------------------------|---------------------------------------------------------------|
| **General Purpose**     | Balanced CPU, memory, and networking (e.g., T2, T3)           |
| **Compute Optimized**   | High-performance processors for compute-heavy workloads (e.g., C5, C6g) |
| **Memory Optimized**    | For in-memory databases and big data (e.g., R5, X1e)          |
| **Storage Optimized**   | High-speed storage for large-scale data processing (e.g., I3, D2) |
| **Accelerated Computing** | GPUs and FPGAs for machine learning, gaming, etc. (e.g., P4, G5) |

For learning purposes, **General Purpose (T2.micro)** is recommended since it falls under the **Free Tier** and is sufficient for small workloads.

---

**AWS Regions and Availability Zones (AZs)**

**What Are Regions?**

AWS has data centers in multiple geographic locations globally, known as regions (e.g., North Virginia, Frankfurt, Mumbai).

**Why Regions Matter:**

•	**Data residency**: Store data closer to customers to comply with legal and privacy requirements.

•	**Low latency**: Ensure faster response times for end-users.

**What Are Availability Zones?**

A **region** consists of multiple **Availability Zones (AZs)** — isolated data centers within a region. Distributing applications across AZs improves **high availability** and **fault tolerance**.

---

**Hands-On: Launching Your First EC2 Instance**

**Step 1: Log into AWS Console**

Navigate to the AWS Management Console and search for **EC2** under services.

**Step 2: Launch a New EC2 Instance**

•	Click on **Launch Instance**

•	Provide a name (e.g., my-first-instance)

•	Choose an OS: **Ubuntu 22.04 LTS** (Free Tier eligible)

•	Select **Instance Type**: t2.micro (1 vCPU, 1 GB RAM)

**Step 3: Create a Key Pair**

•	Key pairs allow SSH access.

•	Choose **Create new key pair** (use RSA).

•	Name it (e.g., aws_login) and download the .pem file securely.

**Step 4: Configure Network Settings**

Skip advanced settings for now. The instance will be placed in the default VPC and subnet. You’ll learn VPCs, subnets, and security groups in later modules.

**Step 5: Launch the Instance**

Click **Launch Instance** and wait for the status to become “running.”

---

**Connecting to Your EC2 Instance**

**Using SSH (on Mac/Linux Terminal or PuTTY for Windows):**

```sh
chmod 600 aws_login.pem
ssh -i "aws_login.pem" ubuntu@<your-ec2-public-ip>
```

You’re now inside your cloud-based Linux server.

---

**Installing Jenkins on EC2 (Your First Application)**

**Step 1: Update Your System**

```sh
sudo apt update
```

**Step 2: Install Java (Required by Jenkins)**

```sh
sudo apt install openjdk-11-jdk -y
```

**Step 3: Add Jenkins Repository and Install**

```sh
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins -y
```

**Step 4: Start Jenkins**

```sh
sudo systemctl start jenkins
sudo systemctl status jenkins
```

---

**Make Jenkins Accessible from Browser**

By default, Jenkins runs on port 8080. To access it externally:

**Step 1: Edit Security Group**

•	Go to **EC2 Dashboard > Instances > Security**

•	Click on the Security Group > **Edit Inbound Rules**

•	Add a rule:

o	Type: Custom TCP

o	Port: 8080

o	Source: Anywhere (0.0.0.0/0)

**Step 2: Open in Browser**

Visit:

```sh
http://<your-ec2-public-ip>:8080
```

Enter the initial admin password (found at /var/lib/jenkins/secrets/initialAdminPassword) and complete the setup.

---

**Best Practices for EC2 Usage**

•	**Always shut down unused instances** to avoid unexpected charges.

•	**Use IAM roles** for better security when accessing AWS resources from EC2.

•	**Use Elastic IPs** for persistent public IP addresses.

•	**Enable Monitoring** using CloudWatch.

•	**Automate deployments** using tools like Terraform, Ansible, or AWS CLI.

---

**Conclusion**

Congratulations. You’ve successfully:

•	Understood the fundamentals of EC2

•	Launched and connected to your first instance

•	Deployed Jenkins and accessed it from your browser

This foundational hands-on experience with EC2 sets the stage for more complex architectures and real-world DevOps workflows. In future articles, we’ll explore networking, storage, scaling, security groups, CI/CD pipelines, and more.

---

**If you found this article helpful, feel free to star this repository and share it with fellow engineers. Contributions are welcome**
