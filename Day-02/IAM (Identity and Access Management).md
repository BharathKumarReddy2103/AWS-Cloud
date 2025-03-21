**AWS IAM**

Welcome to Day 2 of the **AWS Zero to Hero series**. In this session, we'll dive deep into one of the most critical security services in AWS—**Identity and Access Management (IAM)**. This article is designed to provide a clear understanding of how IAM enables **authentication and authorization** in AWS through real-life analogies, practical examples, and hands-on implementation.

Whether you're a DevOps engineer, cloud enthusiast, or someone preparing for AWS certifications or interviews, this guide will give you actionable insights into IAM fundamentals.

---

**Recap of Day 1**

In Day 1, we covered:

•	Cloud Computing Basics

•	Public vs Private Cloud

•	Importance of Public Cloud

•	Why AWS is the market leader

If you missed it, **check out the Day 1 article** to build a strong foundation before continuing.

---

**What is AWS IAM?**

**AWS Identity and Access Management (IAM)** is a foundational AWS service that enables you to:

•	Securely manage access to AWS services and resources.

•	Control who can access what using **authentication** (verifying identity) and **authorization** (granting permissions).

---

**Real-World Analogy: IAM in a Bank**

Imagine a bank:

•	Customers and employees need different levels of access.

•	Some people are allowed at the service desk, while others may access secure vaults or employee systems.

To manage this, the bank implements:

•	**Authentication**: Only verified individuals (e.g., with valid IDs or credentials) are allowed inside.

•	**Authorization**: Determines which zones they can access.

AWS IAM follows the same principle—controlling who can access AWS services and what actions they are allowed to perform.

---

**Why IAM is Crucial in AWS**

Without IAM:

•	Anyone with the **root credentials** (i.e., full access) could accidentally or maliciously delete critical infrastructure like EC2 instances or databases.

•	There would be **no control** over who can perform what actions.

IAM helps establish:

•	**Least privilege access**

•	**Auditability**

•	**Granular permissions**

•	**Security and compliance**

---

**IAM Core Components**

Let’s break down the essential building blocks of IAM:

**Users**

•	Represents **individual identities** (developers, admins, QA engineers).

•	Each user gets login credentials (username/password or programmatic access).

•	Example:

```sh
{
  "UserName": "test-user-501",
  "Access": "Programmatic and AWS Console"
}
```

**Policies**

•	Define **permissions.**

•	Written in JSON.

•	Can be **AWS-managed** or **custom-defined**.

•	Example:

```sh
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "*"
    }
  ]
}
```

**Groups**

•	Logical grouping of users.

•	Attach policies to a group instead of individual users.

•	Ideal for managing permissions at scale.

•	Example groups:

o	Developers

o	QA

o	DevOps

o	Read-Only Users

**Roles**

•	Designed for **applications, services, or users outside AWS** to access AWS resources securely.

•	Temporary credentials.

•	Use cases:

o	Cross-account access

o	EC2 instance access to S3

o	Lambda functions accessing DynamoDB

---

**Hands-On: IAM in Action**

**Step 1: Create a User (Authentication)**

**1.**	Go to IAM Console → Users → Add User

**2.**	Choose test-user-501

**3.**	Select **Console access**

**4.**	Let AWS **auto-generate the password**

**5.**	Require user to reset password at next sign-in

**Step 2: Login as IAM User Without Permissions**

•	You can log in but **cannot access any service** (like EC2, S3).

•	This demonstrates **authentication without authorization**.

**Step 3: Attach Policy (Authorization)**

**1.**	Go back to the root or admin IAM user

**2.**	IAM Console → Users → test-user-501 → Permissions

**3.**	Attach policy: AmazonS3FullAccess

**4.**	Login again as test-user-501

**5.**	Now you can:

o	List S3 buckets

o	Create/Delete S3 buckets

**Step 4: Create a Group**

**1.**	IAM Console → User Groups → Create Group (e.g., developers)

**2.**	Attach AmazonS3FullAccess policy to group

**3.**	Add multiple users to the group

Now any new user added to the group **inherits the group’s permissions**.

**Step 5: Roles (To Be Covered in Depth Later)**

Use when:

•	Applications outside AWS need temporary access.

•	Setting up **cross-account** permissions.

•	Integrating **CI/CD tools like Jenkins or Terraform**.

---

**Best Practices**

•	**Never use the root user for daily tasks**

•	Enable **MFA** (Multi-Factor Authentication) on the root account

•	Use **IAM roles** instead of sharing IAM credentials

•	Grant **least privilege**—only the permissions needed

•	**Group users** by role or team

•	Regularly **rotate credentials**

•	Audit access using **AWS CloudTrail**

---

**Assignment for Learners**

Follow these steps in your own AWS Free Tier account:

**1.**	Create a new IAM user: test-user

**2.**	Login with that user and verify you cannot access anything

**3.**	Grant AmazonS3FullAccess policy to the user

**4.**	Re-login and test access to S3

**5.**	Create a group called developers, assign S3 access

**6.**	Add a new user to the group and validate inherited permissions

---

**Conclusion**

Understanding IAM is the **foundation of cloud security** in AWS. As a DevOps Engineer, mastering IAM allows you to:

•	Control access to infrastructure

•	Reduce risks and errors

•	Enable secure automation workflows

•	Follow security and compliance standards

In the next session, we’ll explore IAM Roles in more detail and apply them in **CI/CD pipelines** with tools like Jenkins and Terraform.

If you found this guide useful, share it with your peers or contribute to the GitHub repo to help others in their AWS journey.

**Stay tuned and happy learning.**
