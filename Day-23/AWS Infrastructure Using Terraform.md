**Automate AWS Infrastructure Using Terraform – Real-World Project for DevOps Engineers**

**Introduction**

As organizations rapidly move towards cloud-native solutions, Infrastructure as Code (IaC) has become a critical component of modern DevOps practices. In this guide, we walk through a real-world project where we automate the provisioning of AWS infrastructure using **Terraform**, a powerful IaC tool. This tutorial is part of the AWS DevOps Zero to Hero series and provides a hands-on approach to building a complete infrastructure stack using Terraform, suitable for beginners and professionals alike.

By the end of this guide, you will have a fully functional AWS environment with:

•	A custom VPC with public subnets

•	EC2 instances running in different Availability Zones

•	An Internet Gateway and route tables for connectivity

•	A Security Group with proper access rules

•	An S3 bucket

•	An Application Load Balancer routing traffic to EC2 instances

•	Terraform best practices and configuration management
________________________________________
**Why Terraform?**

Terraform is a widely-used open-source tool by HashiCorp that allows you to define and manage cloud resources using declarative configuration files. Compared to manual provisioning through the AWS Console or using CLI tools, Terraform provides:

•	Repeatability

•	Version control

•	Automation and scalability

•	Improved collaboration through Git-based workflows
________________________________________
**Prerequisites**

To follow along with this tutorial, you need:

•	An AWS account

•	IAM user with appropriate permissions (preferably Admin for initial practice)

•	Terraform installed on your local machine

•	AWS CLI configured with access keys

•	Visual Studio Code (VS Code) with the official HashiCorp Terraform extension
________________________________________
**Project Architecture**

Below is the high-level architecture of what we’ll be provisioning:

•	A **VPC** with two **public subnets** across different AZs

•	An **Internet Gateway** and **route tables**

•	Two **EC2 instances** with a web server and startup script

•	An **Application Load Balancer** to distribute traffic

•	An **S3 Bucket** (optional but included for demonstration)

•	Terraform-managed **Security Groups**
 
________________________________________
**Step-by-Step Implementation**

**1. Initialize Terraform Project**

Create a new project folder and run:

```sh
terraform init
```

This sets up the Terraform environment and downloads required providers.

**2. Define the Provider**

In provider.tf:

```sh
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }

  required_version = ">= 1.5.0"
}

provider "aws" {
  region = "us-east-1"
}
```
________________________________________
**3. VPC and Subnets**

In main.tf:

```sh
resource "aws_vpc" "main_vpc" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "subnet_1" {
  vpc_id                  = aws_vpc.main_vpc.id
  cidr_block              = "10.0.0.0/24"
  availability_zone       = "us-east-1a"
  map_public_ip_on_launch = true
}

resource "aws_subnet" "subnet_2" {
  vpc_id                  = aws_vpc.main_vpc.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "us-east-1b"
  map_public_ip_on_launch = true
}
```
________________________________________
**4. Internet Gateway and Route Table**

```sh
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.main_vpc.id
}

resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.main_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }
}

resource "aws_route_table_association" "a1" {
  subnet_id      = aws_subnet.subnet_1.id
  route_table_id = aws_route_table.public_rt.id
}

resource "aws_route_table_association" "a2" {
  subnet_id      = aws_subnet.subnet_2.id
  route_table_id = aws_route_table.public_rt.id
}
```
________________________________________
**5. Security Group**

```sh
resource "aws_security_group" "web_sg" {
  name   = "web_sg"
  vpc_id = aws_vpc.main_vpc.id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```
________________________________________
**6. EC2 Instances with User Data**

In user_data_bharath.sh:

```sh
#!/bin/bash
apt update -y
apt install apache2 -y
echo "Welcome to Bharath's Terraform Project" > /var/www/html/index.html
```

In user_data_devOps.sh:

```sh
#!/bin/bash
apt update -y
apt install apache2 -y
echo "Welcome to DevOps Training" > /var/www/html/index.html
```

EC2 Resources:

```sh
resource "aws_instance" "web1" {
  ami                    = "ami-0c02fb55956c7d316" # Ubuntu 20.04 (example)
  instance_type          = "t2.micro"
  subnet_id              = aws_subnet.subnet_1.id
  vpc_security_group_ids = [aws_security_group.web_sg.id]
  user_data              = file("user_data_abhi.sh")
}

resource "aws_instance" "web2" {
  ami                    = "ami-0c02fb55956c7d316"
  instance_type          = "t2.micro"
  subnet_id              = aws_subnet.subnet_2.id
  vpc_security_group_ids = [aws_security_group.web_sg.id]
  user_data              = file("user_data_cloud.sh")
}
```
________________________________________
**7. Load Balancer with Target Group**

```sh
resource "aws_lb" "alb" {
  name               = "web-alb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.web_sg.id]
  subnets            = [aws_subnet.subnet_1.id, aws_subnet.subnet_2.id]
}

resource "aws_lb_target_group" "tg" {
  name     = "web-targets"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.main_vpc.id

  health_check {
    path                = "/"
    protocol            = "HTTP"
    matcher             = "200"
    interval            = 30
    timeout             = 5
    healthy_threshold   = 2
    unhealthy_threshold = 2
  }
}

resource "aws_lb_target_group_attachment" "tg_attach1" {
  target_group_arn = aws_lb_target_group.tg.arn
  target_id        = aws_instance.web1.id
  port             = 80
}

resource "aws_lb_target_group_attachment" "tg_attach2" {
  target_group_arn = aws_lb_target_group.tg.arn
  target_id        = aws_instance.web2.id
  port             = 80
}

resource "aws_lb_listener" "listener" {
  load_balancer_arn = aws_lb.alb.arn
  port              = 80
  protocol          = "HTTP"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.tg.arn
  }
}
```
________________________________________
**8. Outputs**

```sh
output "alb_dns_name" {
  description = "Application Load Balancer DNS Name"
  value       = aws_lb.alb.dns_name
}
```
________________________________________
**Deploying the Infrastructure**

Run the following commands:

```sh
terraform validate
terraform fmt
terraform plan
terraform apply -auto-approve
```

Access the website using the load balancer DNS printed in the output:

```sh
http://<alb_dns_name>
```

Refresh the browser to see load balancing between:

•	Welcome to Bharath's Terraform Project

•	Welcome to DevOps Training
________________________________________
**Cleanup**

To avoid incurring costs:

```sh
terraform destroy -auto-approve
```
________________________________________
**Best Practices**

•	Avoid hardcoding access keys. Use aws configure or environment variables.

•	Split your Terraform code into logical modules.

•	Store the terraform.tfstate file in a remote backend like S3 with state locking (e.g., DynamoDB).

•	Always validate and plan before applying changes.

•	Add meaningful tags to every resource for traceability and cost allocation.
________________________________________
**Real-World Use Cases**

•	Automating staging and production environments for web applications

•	Provisioning consistent infrastructure across regions and accounts

•	CI/CD pipeline integration for auto-deploying cloud resources

•	Freelancing projects involving infrastructure setup for client websites or applications
________________________________________
**Conclusion**

This project demonstrates the power and simplicity of Terraform in building a real-world AWS infrastructure. By following these steps, you gain hands-on experience that closely reflects what DevOps Engineers do in production environments. Whether you're preparing for interviews, building a portfolio, or delivering freelance projects—this example is a must-have in your Terraform learning journey.
