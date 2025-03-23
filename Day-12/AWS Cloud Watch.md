**AWS CloudWatch:**

**Introduction**

In modern DevOps and Cloud environments, monitoring is a non-negotiable part of managing scalable and resilient systems. AWS CloudWatch is a powerful observability service that provides real-time insights into your AWS infrastructure and applications.

This article, based on Day 12 of the AWS Zero to Hero series, dives deep into AWS CloudWatch. It covers essential theory, features, use cases, and real-time demonstrations including configuring alarms, analyzing logs, and understanding default and custom metrics.

Whether you're preparing for interviews, working on AWS infrastructure, or building production-grade applications, this guide will give you a solid foundation in CloudWatch.

---

**What is AWS CloudWatch?**

AWS CloudWatch is essentially the **gatekeeper** or **watchman** of your AWS environment. It continuously monitors your AWS resources and applications, logging activities, collecting metrics, triggering alarms, and offering dashboards for visualization.

**Analogy:**

Imagine CloudWatch as a security guard watching your house (the cloud). You can ask it, “What happened when I was away?” CloudWatch gives you a complete log of every significant activity.

---

**Why CloudWatch Matters in Real-World DevOps**

•	**Monitoring**: Real-time monitoring of infrastructure and applications.

•	**Alerting**: Set alarms based on thresholds to receive proactive notifications.

•	**Logging**: Stores logs for audit and debugging purposes.

•	**Visualization**: Offers dashboards for data visualization.

•	**Cost Optimization & Auto Scaling**: Integrates with services like AWS Lambda and Auto Scaling groups to take automated actions.

---

**Key Concepts & Features**

**1. Metrics**

Metrics are quantitative data points that help you understand your system's performance. Examples include:

•	CPU Utilization

•	Memory Usage

•	API Request Count

•	Network In/Out Bytes

CloudWatch automatically collects over 1000+ metrics across various services.

```sh
Example:
CPUUtilization on EC2 Instance A = 80% (collected every 5 minutes by default)
```

**2. Alarms**

CloudWatch Alarms allow you to take actions when a metric breaches a defined threshold.

**Example Use Case:**

•	Trigger an email notification when CPU utilization exceeds 50%.

•	Integrate with Auto Scaling to spin up new EC2 instances.

```sh
Alarm Rule:
If CPUUtilization >= 50% for 1 minute → Trigger Email via SNS
```

**3. Logs and Log Groups**

CloudWatch Logs store system and application logs. Logs are automatically grouped based on services (e.g., CodeBuild, Lambda).

**Example:**

If you build a project using AWS CodeBuild, CloudWatch automatically creates a log group that records the entire build history, including Docker builds and errors.

```sh
aws logs describe-log-groups
aws logs get-log-events --log-group-name "your-log-group"
```

**4. Dashboards**

Dashboards in CloudWatch allow you to visualize metrics in real-time using charts and graphs. You can display CPU usage, network stats, and more in a single view.

**5. Custom Metrics**

By default, CloudWatch doesn’t track everything (e.g., memory usage). You can create **custom metrics** using the CloudWatch API or the AWS CLI.

```sh
# Example Python Snippet to Push Custom Metric
cloudwatch.put_metric_data(
  Namespace='Custom',
  MetricData=[{
    'MetricName': 'MemoryUsage',
    'Value': 75.5,
    'Unit': 'Percent'
  }]
)
```

---

**Real-World Demo: CPU Utilization Monitoring on EC2**

**Scenario:**

You have an EC2 instance and want to:

**1.**	Monitor CPU usage.

**2.**	Trigger an alarm if usage exceeds 50%.

**3.**	Send a notification via email using SNS.

**Step-by-Step Demo:**

**1. Launch EC2 Instance**

•	Ubuntu OS (T2.micro)

•	Enable detailed monitoring (1-minute granularity)

**2. Simulate CPU Load**

Use a Python script (cpu_spike.py) to spike the CPU.

```sh
import time
while True:
    for i in range(10**6):
        pass
    time.sleep(1)
```

**3. Create CloudWatch Alarm**

•	Metric: CPUUtilization

•	Threshold: >= 50%

•	Evaluation Period: 1 minute

•	Notification: Email via SNS Topic

**4. Create SNS Topic and Subscription**

```sh
aws sns create-topic --name cloudwatch-topic
aws sns subscribe --topic-arn arn:aws:sns:region:account-id:cloudwatch-topic --protocol email --notification-endpoint your-email@example.com
```

**5. Receive Email Notification**

Check your inbox or spam/promotions tab. You'll receive an email like:

“Hey Team, this is an automated notification from CloudWatch. Your EC2 instance CPU has spiked above 50%. Please investigate.”

---

**Best Practices for CloudWatch in DevOps**

•	**Always enable detailed monitoring** for critical resources.

•	**Use average metrics** in alarms for better reliability in production.

•	**Group logs effectively** to debug specific applications or stages.

•	**Automate alarm responses** using AWS Lambda or Step Functions.

•	**Visualize key metrics** in dashboards to track overall health.

•	**Use custom metrics** to track app-specific performance like memory, disk I/O, etc.

---

**Conclusion**

**AWS CloudWatch** is a foundational service for any DevOps or Cloud Engineer. From infrastructure health to application performance, it provides comprehensive observability features that integrate seamlessly with other AWS services.

Make sure to not just understand the theory but also **practice demos**. This hands-on experience will prepare you for real-world scenarios and interviews.

“Metrics tell you what’s happening. Alarms help you take action. Logs give you the why.”

---

**AWS CloudWatch Custom Metrics: Going Beyond Defaults**

Follow-up to **above AWS CloudWatch article**

**Introduction**

While AWS CloudWatch offers over 1000+ default metrics across various services, there are critical scenarios where those defaults aren’t enough—especially when you want to monitor application-specific parameters like **memory usage, disk space, or business KPIs.**

That’s where **Custom Metrics** come in. With custom metrics, you can push your own monitoring data to AWS CloudWatch, opening doors to more granular and insightful observability across your applications and infrastructure.

This guide explains what custom metrics are, why they're essential, how to implement them using Python, and best practices for DevOps Engineers.

---

**What Are Custom Metrics in AWS CloudWatch?**

**Custom Metrics** are user-defined metrics that you manually push to CloudWatch from your applications or scripts.

**Why Use Custom Metrics?**

Default metrics don’t cover everything. For example:

•	EC2 instances do **not** provide memory utilization by default.

•	You might want to monitor **queue lengths, request durations, user sign-ups per hour,** etc.

•	Application-specific metrics like **errors per second, cache hit ratio**, etc., are not tracked unless explicitly sent.

---

**Key Use Cases for Custom Metrics**

•	**Memory Utilization on EC2**

Track free/used memory not covered by default EC2 metrics.

•	**Disk Usage**

Monitor disk space used/free on Linux or Windows instances.

•	**Business KPIs**

E.g., Orders per minute, conversion rates, failed transactions.

•	**Application Health**

Track custom HTTP codes, response times, and error rates.

---

**How Custom Metrics Work**

Custom metrics are pushed into CloudWatch using the **PutMetricData API**. You can send data:

•	From your EC2 instances

•	From Lambda functions

•	From on-premise servers using the AWS CLI or SDK

---

**Step-by-Step Guide: Create Custom Metrics Using Python**

**Prerequisites:**

•	EC2 instance with **Python 3** and **boto3** installed

•	IAM role attached with cloudwatch:PutMetricData permission

•	AWS CLI configured

**Step 1: Install Boto3**

```sh
pip install boto3
```

**Step 2: Python Script to Push Memory Usage as a Custom Metric**

```sh
# custom_memory_metric.py

import boto3
import psutil
import time

cloudwatch = boto3.client('cloudwatch', region_name='us-east-1')

def publish_memory_usage():
    memory = psutil.virtual_memory()
    used_memory_percent = memory.percent

    cloudwatch.put_metric_data(
        Namespace='CustomEC2Metrics',
        MetricData=[
            {
                'MetricName': 'MemoryUtilization',
                'Dimensions': [
                    {
                        'Name': 'InstanceId',
                        'Value': 'i-0abc123def456ghi'  # Replace with your EC2 instance ID
                    },
                ],
                'Unit': 'Percent',
                'Value': used_memory_percent
            },
        ]
    )
    print(f"Pushed memory utilization: {used_memory_percent}%")

while True:
    publish_memory_usage()
    time.sleep(60)  # Push data every 1 minute
```

**Step 3: Run the Script**

```sh
python3 custom_memory_metric.py
```

This will push the memory utilization metric to CloudWatch every 60 seconds.

---

**View Custom Metrics in CloudWatch**

**1.**	Go to the AWS Console → CloudWatch

**2.**	Navigate to **Metrics** → Select **CustomEC2Metrics**

**3.**	You’ll see MemoryUtilization under that namespace

**4.**	You can now:

o	Create dashboards

o	Set alarms

o	Visualize trends

---

**Set an Alarm on Custom Metric (Optional)**

**1.**	Go to **Alarms** → Create Alarm

**2.**	Choose CustomEC2Metrics > MemoryUtilization

**3.**	Set a threshold (e.g., > 80%)

**4.**	Choose an action (e.g., email via SNS)

**5.**	Create and test your alarm

---

**Best Practices for Using Custom Metrics**

•	**Use Namespaces Wisely**

Group similar metrics under a consistent Namespace like CustomAppMetrics.

•	**Use Dimensions**

Add identifiers (e.g., InstanceId, Environment) for filtering and grouping.

•	**Control Data Frequency**

Avoid unnecessary data floods. CloudWatch charges per data point.

•	**Optimize Cost**

Use standard resolution (1-minute) for critical metrics. Avoid high-resolution unless required.

•	**Clean Up**

Remove scripts or automation pushing metrics when no longer needed.

---

**Conclusion**

Custom metrics unlock the full potential of CloudWatch, especially when you're monitoring beyond what AWS offers out of the box. As a DevOps Engineer, mastering custom metrics gives you a competitive edge in handling real-world monitoring challenges.

Start simple—track memory usage, disk space, or error counts—and gradually evolve your observability stack.
