**AWS Cloud Cost Optimization Using Lambda**

**Introduction**

As organizations shift from traditional on-premise infrastructures to cloud platforms like AWS, managing costs becomes a critical responsibility. While the cloud offers scalability, agility, and reduced infrastructure overhead, these benefits can quickly be overshadowed by poor cost management practices. In this article, we’ll explore how **DevOps and Cloud Engineers** can effectively optimize cloud costs using **AWS Lambda** and **Python (Boto3)** to automate the cleanup of unused EBS snapshots.

This real-world project showcases a **practical implementation** of cost optimization that can be added to your resume or GitHub portfolio—making it valuable for recruiters, hiring managers, and open-source contributors alike.
________________________________________
**Why Cloud Cost Optimization Matters**

**Key Reasons Organizations Move to the Cloud**

**1.	Reduced Infrastructure Overhead**: No need to purchase physical servers or manage data centers.

**2.	Cost Efficiency**: Pay-as-you-go pricing models help reduce unnecessary spending—if used properly.

However, merely shifting to the cloud doesn't guarantee cost savings. Without active monitoring and automation, cloud costs can escalate due to idle or forgotten resources.

**Common Cost Leak Scenarios**

•	**Stale EBS Snapshots**: Snapshots taken regularly but never deleted.

•	**Detached EBS Volumes**: Volumes left unattached to any EC2 instance.

•	**Unused S3 Buckets or EKS Clusters**: Leftover resources from testing or deprecated features.
________________________________________
**Use Case: Automating Cleanup of Stale EBS Snapshots**

**Problem Statement**

Developers often create **EBS snapshots** as backups of EC2 volumes. Over time, these snapshots may no longer be needed—especially if the volume or EC2 instance has been deleted. Since AWS charges for each GB of snapshot storage, these unused snapshots can lead to **unnecessary costs**.

**Goal**

Automatically detect and delete **stale EBS snapshots**—snapshots linked to:

•	Deleted volumes

•	Volumes not attached to any EC2 instance
________________________________________
**Solution Architecture**

We’ll use the following AWS services:
•	**AWS Lambda**: For running Python scripts automatically.

•	**Boto3**: AWS SDK for Python to interact with EC2 resources.

•	**CloudWatch Events (EventBridge)**: To schedule the Lambda function.

**Workflow**

```sh
EBS Snapshots -> Lambda (Python + Boto3) -> AWS EC2 API
                                      ↓
                               Identify Stale Snapshots
                                      ↓
                               Delete Unused Snapshots
                                      ↓
                             Scheduled via CloudWatch Events
```
________________________________________
**Implementation: Lambda Function for Snapshot Cleanup**

**Step 1: Create EC2 Instance & Snapshot**

•	Launch an EC2 instance with default EBS volume.

•	Take manual snapshots of the EBS volume.

**Step 2: Delete EC2 Instance & Volume**

•	Terminate the EC2 instance.

•	Ensure the volume is also deleted, leaving the snapshot orphaned.

**Step 3: Write and Deploy Lambda Function**

**Required IAM Permissions**

Create and attach a policy to the Lambda execution role with the following permissions:

•	ec2:DescribeInstances

•	ec2:DescribeVolumes

•	ec2:DescribeSnapshots

•	ec2:DeleteSnapshot

**Python Code Overview (EBS_stale_snapshots.py)**

```sh
import boto3

ec2 = boto3.client('ec2')

# Get active instance IDs
active_instances = set()
reservations = ec2.describe_instances()['Reservations']
for res in reservations:
    for inst in res['Instances']:
        active_instances.add(inst['InstanceId'])

# Get all volumes
volume_ids = set()
volumes = ec2.describe_volumes()['Volumes']
for vol in volumes:
    for attachment in vol.get('Attachments', []):
        volume_ids.add(vol['VolumeId'])

# Get and evaluate all snapshots
snapshots = ec2.describe_snapshots(OwnerIds=['self'])['Snapshots']
for snapshot in snapshots:
    volume_id = snapshot.get('VolumeId')
    if not volume_id or volume_id not in volume_ids:
        try:
            ec2.delete_snapshot(SnapshotId=snapshot['SnapshotId'])
            print(f"Deleted Snapshot: {snapshot['SnapshotId']}")
        except Exception as e:
            print(f"Error: {str(e)}")
```

**Step 4: Deploy and Test**

**1.**	Open AWS Lambda console.

**2.**	Create a new function and paste the Python code.

**3.**	Attach the custom IAM role with permissions.

**4.**	Manually test the Lambda.

**5.**	Verify if stale snapshots are successfully deleted.
________________________________________
**Automate with CloudWatch Events**

Instead of running the Lambda manually:

**1.**	Go to **CloudWatch > Rules > Create Rule**.

**2.**	Choose a schedule (e.g., every 24 hours).

**3.**	Set the target to the Lambda function.

**4.**	Confirm and enable the rule.
________________________________________
**Best Practices**

•	**Least Privilege Principle**: Ensure Lambda roles have only necessary permissions.

•	**Tagging**: Encourage developers to tag resources for easy identification and cleanup.

•	**Snapshot Lifecycle Policies**: Use automated retention policies where applicable.

•	**Backup Compliance**: Confirm that business-critical backups are retained before deletion.

•	**Logging**: Store Lambda logs in CloudWatch for auditing and troubleshooting.
________________________________________
**Real-World Applications**

•	**Enterprise Cloud Governance**: Part of cost control and compliance frameworks.

•	**DevOps Automation**: Integrated with CI/CD pipelines to manage ephemeral environments.

•	**Startups & SMBs**: Helps control AWS billing in budget-sensitive environments.

•	**Resume Project**: Great practical project to showcase on GitHub or during interviews.
________________________________________
**Additional Use Cases**

You can extend this architecture to:

•	Clean up **unattached EBS volumes**

•	Delete **unused EKS clusters**

•	Remove **unused S3 buckets or RDS snapshots**
________________________________________
**Conclusion**

Cloud cost optimization is not a one-time activity but an ongoing DevOps practice. Automating snapshot cleanup using AWS Lambda and Boto3 is just one way to reduce unnecessary cloud spending. By implementing such solutions, DevOps and Cloud Engineers not only save money but also demonstrate initiative and cloud maturity—skills highly valued in today’s job market.
________________________________________

**If you found this useful, star the repository and share it with your network.**
