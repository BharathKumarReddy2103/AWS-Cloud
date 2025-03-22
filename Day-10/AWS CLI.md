# Mastering AWS CLI: Automate AWS Infrastructure Like a Pro

Welcome to Day 10 of the **AWS Zero to Hero** series. In this guide, we’ll explore one of the most essential tools every DevOps and Cloud engineer must know—**AWS CLI (Command Line Interface)**.

As AWS becomes a foundational platform in modern cloud infrastructure, learning how to automate AWS services using CLI tools is critical. Whether you're managing infrastructure manually or want to build scalable, repeatable DevOps workflows, this article will walk you through everything you need to know to get started with AWS CLI.

---

## What is AWS CLI?

**AWS CLI (Command Line Interface)** is an open-source tool built by AWS that allows users to interact with AWS services through a terminal instead of the web UI.

- Written in Python.
- Interfaces with AWS services using **API requests**.
- Supports scripting, automation, and integration with DevOps workflows.

---

## Why Use AWS CLI Over the AWS Console?

While the AWS Management Console is great for beginners and one-off configurations, it lacks automation and scalability.

**Challenges with AWS UI:**

- Manual and time-consuming for repeated tasks.
- Not suitable for infrastructure as code (IaC).
- Not DevOps or CI/CD friendly.

**Advantages of AWS CLI:**

- Automates repetitive tasks using shell or Python scripts.
- Works well with tools like Terraform, CloudFormation, and CI/CD pipelines.
- Ideal for quick operations and DevOps workflows.

---

## Understanding AWS APIs

Every AWS service exposes a set of **APIs** that allow developers and DevOps engineers to:

- Create, modify, and delete AWS resources.
- Interact with services programmatically.
- Build automation pipelines.

**CLI vs API vs UI:**

| Interface | Use Case | Automation | Best For |
|----------|----------|------------|----------|
| UI | Visual interaction | Manual | Beginners, debugging |
| API | Direct integration | Full automation | Developers |
| CLI | Command-based | Semi-automated | DevOps & scripting |

**Example:**
Creating an S3 bucket via API:

```sh
POST https://api.aws.com/s3/create
{
  "bucketName": "my-bucket",
  "versioning": true
}
```

With AWS CLI:

```sh
aws s3 mb s3://my-bucket --region us-east-1
```

---

**Installing AWS CLI**

Follow these steps to install the AWS CLI:

**On macOS (Homebrew):**

```sh
brew install awscli
```

**On Linux:**

```sh
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

**On Windows:**

•	Download MSI installer from **AWS CLI official docs** (https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).

•	Run the installer and follow the steps.

Verify installation:

```sh
aws --version
```

---

**Configuring AWS CLI**

To connect your CLI to your AWS account:

```sh
aws configure
```

You will be prompted to enter:

•	Access Key ID

•	Secret Access Key

•	Default region (e.g., us-east-1)

•	Default output format (json, yaml, table, etc.)

**Where to find your credentials?**

•	Navigate to AWS Console > IAM > Users > Security credentials.

•	Generate or copy your **Access Key and Secret**.

Note: Avoid using root user access keys in production. Use IAM users with appropriate permissions.

---

**Running Your First AWS CLI Commands**

**List S3 Buckets:**

```sh
aws s3 ls
```

**Create an EC2 Instance:**

```sh
aws ec2 run-instances \
  --image-id ami-0abcdef1234567890 \
  --instance-type t2.micro \
  --key-name my-key-pair \
  --security-group-ids sg-0123456789abcdef0 \
  --subnet-id subnet-0123456789abcdef0 \
  --region us-east-1 \
  --count 1 \
  --output json
```

Replace ami-id, key-name, security-group-id, and subnet-id with values from your own AWS account.

**Check AWS CLI Docs:**

AWS provides complete command references:

•	**AWS CLI Command Reference** (https://docs.aws.amazon.com/cli/latest/)

•	**S3 CLI Reference** (https://docs.aws.amazon.com/cli/latest/reference/s3/)

•	**EC2 CLI Reference** (https://docs.aws.amazon.com/cli/latest/reference/ec2/)

---

**Real-World Use Cases**

As a DevOps Engineer, you’ll use AWS CLI for:

•	Quick queries (e.g., list S3 buckets, EC2 instances).

•	Simple automation tasks.

•	Running scripts in CI/CD pipelines.

•	Deployments in shell or Jenkins pipelines.

•	Generating resource outputs for reporting.

---

**Best Practices for AWS CLI**

•	**Use IAM roles** instead of access keys when possible (especially on EC2).

•	**Avoid storing keys in scripts**. Use AWS profiles and environment variables.

•	**Validate commands** using --dry-run where available.

•	**Automate** using shell scripts or tools like Makefile, Ansible, or GitHub Actions.

•	**Use aliases** for frequently used commands to save time.

---

**Conclusion**

The AWS CLI is a powerful tool that bridges the gap between manual cloud operations and full automation. It allows DevOps engineers to:

•	Automate infrastructure tasks

•	Improve workflow efficiency

•	Integrate with other DevOps tools

If you're serious about cloud engineering, **learning AWS CLI is non-negotiable**. It’s your gateway to automation, CI/CD, and infrastructure as code.

**Next Up**: Learn AWS CloudFormation Templates and understand how CLI compares to Terraform and CDK for large-scale deployments.

If you found this guide helpful, please consider sharing it, giving the repo a star, and following for more DevOps and Cloud engineering tutorials.
