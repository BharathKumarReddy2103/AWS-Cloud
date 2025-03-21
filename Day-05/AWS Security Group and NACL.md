**AWS VPC Security:Security Groups and Network ACLs (NaCl)**

In this article, we’ll explore one of the most critical aspects of working with Amazon Web Services (AWS) — securing your applications within a Virtual Private Cloud (VPC) using **Security Groups** and **Network Access Control Lists (NaCl)**. This article is part of the AWS Zero to Hero series and builds upon the foundational concepts introduced in previous sessions on VPCs.

---

**Introduction**

As AWS users, ensuring the security of our infrastructure is a shared responsibility. While AWS provides built-in tools and services for protection, it's up to DevOps and Cloud Engineers to configure them correctly. In this article, we’ll focus on two fundamental tools:

•	**Security Groups** — virtual firewalls for your EC2 instances.

•	**Network Access Control Lists (NaCl)** — subnet-level traffic controllers.

Both serve different but complementary purposes in securing workloads deployed in AWS.

---

**Understanding VPC Security Layers**

A Virtual Private Cloud (VPC) is a logically isolated section of the AWS Cloud where you can launch resources in a virtual network you define.

**Key Security Layers in a VPC:**

•	**Internet Gateway**: Enables communication between instances and the internet.

•	**Route Tables**: Direct network traffic.

•	**Public and Private Subnets**: Separate external-facing from internal-facing resources.

•	**Security Groups**: Control instance-level traffic.

•	**NaCls**: Control subnet-level traffic.

---

**Security Groups: Instance-Level Protection**

Security Groups act as virtual firewalls attached to EC2 instances. They control **inbound and outbound traffic** at the **instance level**.

**Inbound and Outbound Rules**

•	**Inbound Traffic**: Refers to incoming traffic into your EC2 instance (e.g., a user accessing a web app).

•	**Outbound Traffic**: Refers to outgoing traffic from your instance (e.g., the app accessing an external API).

**Default Behavior**:

•	**Inbound**: All traffic is denied by default.

•	**Outbound**: All traffic is allowed by default, except port 25 (blocked to prevent email spam).

```sh
# Start a simple Python HTTP server
python3 -m http.server 8000
```

To access this app via browser, you must allow port 8000 in the security group.

**Use Case: Accessing Jenkins**

During a previous session, Jenkins was deployed on an EC2 instance. Despite successful deployment, the application was inaccessible because port 8080 wasn’t allowed in the security group. Once allowed, traffic flowed seamlessly.

---

**NaCl: Subnet-Level Security**

**Network Access Control Lists (NaCls)** provide stateless, rule-based control over traffic at the subnet level.

**Key Characteristics:**

•	Stateless (rules are evaluated independently for each request).

•	Rules have explicit **allow** and **deny** options.

•	Evaluated in order (lowest rule number takes precedence).

•	Affect all instances in the subnet.

**Comparison with Security Groups**

| Feature              | Security Groups      | NaCl                      |
|----------------------|----------------------|----------------------------|
| Level                | Instance Level        | Subnet Level               |
| Stateful             | Yes                   | No                         |
| Allow/Deny           | Allow only            | Allow and Deny             |
| Evaluation Order     | All rules             | Lowest rule number first   |
| Use Case             | Fine-grained control  | Broad traffic control      |

**Real-World Scenario**

If a development team mistakenly opens **all ports** on an EC2 instance, NaCl can override and block risky traffic at the subnet level — securing all instances under that subnet.

---

**Hands-On Lab: Deploying and Securing a Python App on EC2**

**Step 1: Create a Custom VPC**

•	Use **“VPC and more”** wizard in AWS Console.

•	Configure:

o	CIDR block: 10.0.0.0/16

o	Public and private subnets

o	Internet Gateway and Route Tables

**Step 2: Launch EC2 Instance in Public Subnet**

•	Select **Ubuntu t2.micro**

•	Assign the EC2 instance to the **public subnet** of your custom VPC

•	Enable **Auto-assign Public IP**

•	Create or use an existing **Security Group**

**Step 3: Run Python HTTP Server**

SSH into the instance:

```sh
ssh -i aws-login.pem ubuntu@<public-ip>
sudo apt update
python3 -m http.server 8000
```

Visit http://<public-ip>:8000 in your browser.

**Step 4: Configure Security Group and NaCl**

**Allow traffic in Security Group:**

•	Inbound Rule:

o	Type: Custom TCP

o	Port: 8000

o	Source: 0.0.0.0/0

**Block traffic in NaCl**:

•	Add **DENY rule** for TCP port 8000 with **lower rule number** than ALLOW rule.

```sh
Rule #100: Deny TCP port 8000 from 0.0.0.0/0
Rule #200: Allow all traffic
```
NaCl will block the traffic, overriding the Security Group.

---

**Best Practices for VPC Security**

•	Always use **least privilege principle** for traffic control.

•	**Avoid allowing all traffic (0.0.0.0/0)** unless necessary.

•	Prefer **custom VPCs** in production environments.

•	Use **NaCl for broad subnet-level restrictions** and Security Groups for **granular control**.

•	Regularly audit **inbound/outbound rules**.

---

**Conclusion**

Understanding and implementing VPC security is foundational for every DevOps or Cloud Engineer working on AWS. Security Groups and NaCl act as vital layers of defense, ensuring secure communication within and outside your AWS infrastructure.

Mastering these concepts will not only help you design secure environments but also give you an edge in interviews, freelance projects, and enterprise-level DevOps work.
