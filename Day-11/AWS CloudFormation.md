**AWS CloudFormation Templates: Infrastructure as Code with CFT**

**Introduction**

In modern DevOps and Cloud environments, managing infrastructure efficiently and reproducibly is crucial. AWS CloudFormation Templates (CFTs) provide a powerful way to define and provision AWS infrastructure using code, following the principles of Infrastructure as Code (IaC).

This article is part of the AWS Zero to Hero series and is intended for DevOps engineers, cloud practitioners, and anyone looking to master AWS-native IaC tools. We will deep dive into CloudFormation Templates, compare them with other tools like AWS CLI and Terraform, explore their structure, and demonstrate real-world use cases.

---

**What is AWS CloudFormation?**

**AWS CloudFormation** is an infrastructure automation service that helps you define AWS resources using templates written in YAML or JSON. These templates are deployed as **stacks**, which represent a collection of AWS resources that can be created, updated, or deleted as a single unit.

**Why Use CloudFormation?**

•	Automate infrastructure provisioning

•	Enable version control for infrastructure

•	Apply consistent configurations across environments

•	Support CI/CD integration

•	Ensure compliance through auditing and reviews

---

**Infrastructure as Code (IaC)**

**Infrastructure as Code (IaC)** means managing and provisioning cloud infrastructure through code instead of manual processes. CFTs follow this paradigm by allowing users to define infrastructure declaratively.

**Key Principles of IaC**

•	**Declarative Nature**: Define what to deploy, not how. What you see in the template is what you get in the infrastructure.

•	**Version Controlled**: Templates can be stored in Git or S3, enabling history tracking and collaboration.

•	**Reusable**: Parameterized templates allow reuse across multiple environments.

---

**CloudFormation vs AWS CLI vs Terraform**

| Feature             | AWS CLI             | CloudFormation (CFT) | Terraform            |
|---------------------|---------------------|-----------------------|----------------------|
| Automation          | Manual scripting    | Fully automated IaC   | Fully automated IaC  |
| Multi-cloud Support | No                  | AWS only              | Yes                  |
| Reusability         | Limited             | High (with parameters and mappings) | High                  |
| Drift Detection     | No                  | Yes                   | Yes                  |
| Complexity          | Low                 | Moderate              | Moderate to High     |

**When to Use What?**

•	**AWS CLI**: For quick, one-off tasks like listing S3 buckets.

•	**CloudFormation**: For provisioning full infrastructure stacks within AWS.

•	**Terraform**: For multi-cloud environments or complex infrastructure management.

---

**Components of a CloudFormation Template**

Templates can be written in **YAML** (preferred for readability and comments) or **JSON**. A typical CloudFormation template includes:

```sh
AWSTemplateFormatVersion: '2010-09-09'
Description: Template to create an S3 bucket
Metadata:
  Author: Bharath Kumar Reddy
Parameters:
  EnvironmentType:
    Type: String
    Default: dev
    AllowedValues:
      - dev
      - staging
      - prod
    Description: Environment in which resources will be deployed
Resources:
  MyS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-demo-bucket-dev
      VersioningConfiguration:
        Status: Enabled
Outputs:
  BucketNameOutput:
    Description: Name of the created S3 bucket
    Value: !Ref MyS3Bucket
```

---

**Practical Demo 1: Creating an S3 Bucket with CFT**

Here’s how you can provision an S3 bucket using a CloudFormation template.

**1. Write the Template (s3-bucket.yaml)**

Save the following YAML content into a file named s3-bucket.yaml.

```sh
AWSTemplateFormatVersion: '2010-09-09'
Description: CloudFormation template to create an S3 bucket with versioning enabled
Resources:
  MyS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-demo-bucket-dev-bkr
      VersioningConfiguration:
        Status: Enabled
Outputs:
  BucketNameOutput:
    Description: Name of the created S3 bucket
    Value: !Ref MyS3Bucket
```

**2. Upload to CloudFormation**

**1.**	Go to the AWS Management Console

**2.**	Navigate to **CloudFormation > Create Stack > With new resources (standard)**

**3.**	Choose “Upload a template file” and select s3-bucket.yaml

**4.**	Click **Nex**t, name the stack (e.g., demo-s3-stack)

**5.**	Click through to **Submit**

**3. Validate and Verify**

•	Wait for the stack status to become CREATE_COMPLETE

•	Go to the **S3 console** and confirm the bucket was created

•	Check that **versioning** is enabled in the bucket properties

---

**Practical Demo 2: Creating an EC2 Instance Using AWS CloudFormation (CFT)**

**Introduction**

Amazon EC2 (Elastic Compute Cloud) is one of the most widely used services on AWS, offering scalable virtual servers in the cloud. Provisioning EC2 instances manually through the AWS Console or CLI can be repetitive and error-prone. This is where **AWS CloudFormation Templates (CFT)** come in.

In this article, we’ll walk you through creating an EC2 instance using CloudFormation, explaining the key sections of the template, recommended practices, and how to deploy the template using the AWS Console.

This article is ideal for DevOps Engineers and Cloud professionals looking to adopt Infrastructure as Code (IaC) for consistent, automated EC2 provisioning.

---

**Why Use CloudFormation to Provision EC2?**

•	Automates and standardizes EC2 instance creation

•	Supports version-controlled infrastructure

•	Allows parameterization and reuse across environments

•	Enables audits, rollback, and drift detection

•	Simplifies integration into CI/CD pipelines

---

**Prerequisites**

Before you begin, ensure that you have:

•	An **AWS account**

•	**IAM permissions** to create EC2 instances, key pairs, and security groups

•	**Visual Studio Code** with the **YAML** and **AWS Toolkit** extensions (recommended)

•	A **key pair** created in the region where you'll launch the instance

---

**CloudFormation Template to Launch an EC2 Instance**

Below is a **sample YAML template** to create an EC2 instance using AWS CloudFormation:

```sh
AWSTemplateFormatVersion: '2010-09-09'
Description: CloudFormation Template to create an EC2 instance in the default VPC

Parameters:
  KeyName:
    Description: Name of an existing EC2 KeyPair to enable SSH access
    Type: AWS::EC2::KeyPair::KeyName
  InstanceType:
    Description: EC2 instance type
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - t2.small
      - t2.medium
    ConstraintDescription: Must be a valid EC2 instance type.
  ImageId:
    Description: AMI ID for the instance
    Type: AWS::EC2::Image::Id
    Default: ami-0c02fb55956c7d316  # Amazon Linux 2 in us-east-1

Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      KeyName: !Ref KeyName
      ImageId: !Ref ImageId
      Tags:
        - Key: Name
          Value: CFT-EC2-Demo

Outputs:
  InstanceId:
    Description: Instance ID of the created EC2 instance
    Value: !Ref MyEC2Instance
  PublicIP:
    Description: Public IP address of the instance
    Value: !GetAtt MyEC2Instance.PublicIp
```

---

**Step-by-Step Deployment Using AWS Console**

**Step 1: Save the Template**

•	Save the above content in a file named ec2-instance.yaml.

**Step 2: Upload to CloudFormation**

**1.**	Login to the **AWS Console**

**2.**	Navigate to **CloudFormation** service

**3.**	Click **Create stack → With new resources (standard)**

**4.**	Select **Upload a template file**

**5.**	Upload ec2-instance.yaml

**6.**	Click **Next**

**Step 3: Configure Stack**

•	Enter **Stack Name**: ec2-demo-stack

•	Enter parameter values:

o	**KeyName**: Use a valid key pair from your AWS region

o	**InstanceType**: Choose t2.micro, t2.small, or t2.medium

o	**ImageId**: Leave default or choose a valid AMI for your region

**Step 4: Deploy Stack**

•	Click **Next** through the remaining screens

•	Acknowledge any IAM changes (if applicable)

•	Click **Create Stack**

---

**Verify the Deployment**

After the stack is created:

•	Go to the **EC2 Dashboard**

•	Locate the new instance (tagged as CFT-EC2-Demo)

•	Check the **Public IP** and **Instance ID** (also visible in stack outputs)

---

**Real-World Use Case**

A DevOps team needs to spin up test environments for every feature branch. Using a CloudFormation template like this, they can automate instance provisioning directly from their CI/CD pipeline (e.g., GitLab CI/CD, GitHub Actions, or Jenkins).

**Best Practices**

•	Use **YAML** over JSON for readability and comments

•	Store templates in **version control** (GitHub, GitLab, CodeCommit)

•	Use **Parameters and Mappings** to make templates reusable

•	Leverage **Drift Detection** to monitor configuration integrity

•	Write **Outputs** for ease of integration with CI/CD pipelines

•	Use **Validation** (aws cloudformation validate-template) before deployment

---

**Developer Tips: Tools & Plugins**

**Recommended VS Code Extensions**

•	**YAML by Red Hat**: Helps with indentation, auto-complete, and schema validation

•	**AWS Toolkit**: Interact with AWS directly from VS Code and preview templates

**CloudFormation Designer**

AWS Console provides a drag-and-drop designer to visually create templates—great for beginners.

---

**Terraform vs CloudFormation: Final Thoughts**

Choose **Terraform** if:

•	You're working in a **multi-cloud** or hybrid environment

•	You want **provider-agnostic** flexibility

•	You need a **mature community and open-source support**

Choose **CloudFormation** if:

•	You're working **only in AWS**

•	You want **tight AWS integration**

•	You want to use **native AWS IaC capabilities**

---

**Conclusion**

**CloudFormation Templates** empower DevOps engineers to manage infrastructure predictably and securely. Whether you're deploying a single EC2 instance or an entire VPC, **CFTs** make infrastructure provisioning scalable and consistent. Mastering **CFTs**, along with tools like Terraform, positions you as a capable DevOps or Cloud Engineer ready for real-world challenges.
