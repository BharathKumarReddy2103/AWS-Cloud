**AWS EKS:**

**Deploying a Real-World Application on AWS EKS with Ingress and ALB Controller**

Amazon Elastic Kubernetes Service (EKS) is a powerful, fully managed Kubernetes service provided by AWS. In this article, we’ll take a deep dive into EKS, explore why it's essential for DevOps Engineers, and walk through the deployment of a real-time 2048 game application using EKS, Fargate, Ingress, and the AWS Application Load Balancer (ALB) Controller.
________________________________________
**Table of Contents**

**1.**	Introduction to AWS EKS

**2.**	Why Choose EKS Over Self-Managed Kubernetes

**3.**	EKS Architecture Overview

**4.**	Deploying a Real-World Application on EKS

**5.**	Understanding Kubernetes Ingress & ALB Controller

**6.**	Step-by-Step Deployment

**7.**	Best Practices & Insights

**8.**	Conclusion
________________________________________
**Introduction to AWS EKS**

Amazon EKS (Elastic Kubernetes Service) is a **fully managed Kubernetes control plane** offered by AWS. It allows users to run Kubernetes clusters without having to manage the complexities of setting up, maintaining, and operating control plane components like the API server, etcd, and schedulers.

**Why EKS Is Important for DevOps Engineers**

•	Reduces operational overhead of managing Kubernetes clusters

•	Integrated with AWS IAM for fine-grained access control

•	Built-in high availability across multiple AZs

•	Secure and scalable deployments using Fargate or EC2
________________________________________
**Why Choose EKS Over Self-Managed Kubernetes**

Deploying Kubernetes clusters manually using tools like kubeadm or kops is often complex, error-prone, and hard to scale.

**Challenges of Self-Managed Clusters**

•	Manual setup of master and worker nodes

•	Installation of control plane components (API server, etcd, scheduler, etc.)

•	Maintenance overhead (certificate renewal, API availability, etc.)

•	Difficult to manage at scale (e.g., 100s of clusters)

**Advantages of EKS**

•	**Managed Control Plane**: AWS handles all control plane components.

•	**High Availability**: Built-in across multiple AZs.

•	**Support for Fargate**: Serverless worker nodes for container workloads.

•	**Integration with AWS ecosystem**: IAM, CloudWatch, Load Balancers, etc.
________________________________________
**EKS Architecture Overview**

**Kubernetes Components in EKS**

•	**Control Plane** (Managed by AWS):

o	API Server

o	etcd

o	Scheduler

o	Controller Manager

•	**Data Plane** (User Managed):

o	Worker Nodes (EC2 or Fargate)

**Deployment Options**

| Option             | Control Plane | Worker Nodes | Use Case                                           |
|--------------------|----------------|---------------|-----------------------------------------------------|
| `kubeadm`          | Self-managed    | Self-managed  | Custom setups, on-premises                         |
| `kops`             | Semi-managed    | Self-managed  | Easier setup on AWS                                |
| `EKS + EC2`        | Managed         | EC2           | When control over OS/runtime is needed             |
| `EKS + Fargate`    | Managed         | Fargate       | Fully serverless, minimal maintenance              |
________________________________________
**Deploying a Real-World Application on EKS**

We will deploy a classic 2048 game application on AWS EKS using the following components:

•	**Deployment**: 2048 app container

•	**Service**: Expose pods internally

•	**Ingress**: Route external traffic into the cluster

•	**ALB Controller**: Automatically creates and manages AWS ALB
________________________________________
**Understanding Kubernetes Ingress & ALB Controller**

**Service Types**

•	**ClusterIP** - Internal only

•	**NodePort** - Accessible via node IP + port

•	**LoadBalancer** - Exposes service using a cloud provider LB (expensive)

**Why Use Ingress?**

Ingress lets you:

•	Route traffic via a single load balancer

•	Define host/path-based routing

•	Reduce costs compared to multiple LBs

**Ingress Controller**

An Ingress resource needs a controller to watch and apply the rules. Common controllers:

•	**nginx**

•	**ALB Ingress Controller (AWS)**

•	**HAProxy**

•	**Traefik**
________________________________________
**Step-by-Step Deployment**

**1. Install Prerequisites**

Ensure you have:

•	kubectl

•	eksctl

•	AWS CLI configured with credentials

```sh
aws configure
eksctl version
kubectl version --client
```

**2. Create EKS Cluster with Fargate**

```sh
eksctl create cluster \
  --name demo-cluster \
  --region us-east-1 \
  --fargate
```

This automatically sets up:

•	VPC with public/private subnets

•	EKS control plane

•	Fargate profiles

**3. Create a Fargate Profile for a Custom Namespace**

```sh
eksctl create fargateprofile \
  --cluster demo-cluster \
  --region us-east-1 \
  --name sample-app \
  --namespace game2048
```

**4. Deploy Application and Ingress**

```sh
kubectl apply -f https://raw.githubusercontent.com/<your-github-repo>/2048-app.yaml
```

This YAML includes:

•	Namespace: game2048

•	Deployment of 2048 app

•	ClusterIP service

•	Ingress resource with ALB annotations

**5. Associate IAM OIDC Provider**

```sh
eksctl utils associate-iam-oidc-provider \
  --cluster demo-cluster \
  --region us-east-1 \
  --approve
```

**6. Create IAM Policy for ALB Controller**

```sh
aws iam create-policy \
  --policy-name ALBIngressControllerIAMPolicy \
  --policy-document file://iam-policy.json
```

**7. Create Service Account with IAM Role**

```sh
eksctl create iamserviceaccount \
  --cluster demo-cluster \
  --namespace kube-system \
  --name aws-load-balancer-controller \
  --attach-policy-arn arn:aws:iam::<ACCOUNT_ID>:policy/ALBIngressControllerIAMPolicy \
  --approve
```

**8. Install ALB Ingress Controller using Helm**

```sh
helm repo add eks https://aws.github.io/eks-charts
helm repo update

helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  --namespace kube-system \
  --set clusterName=demo-cluster \
  --set serviceAccount.create=false \
  --set region=us-east-1 \
  --set vpcId=<your-vpc-id> \
  --set serviceAccount.name=aws-load-balancer-controller
```

**9. Verify Deployment**

```sh
kubectl get ingress -n game2048
```

You will get an external URL from ALB. Open it in your browser to access the 2048 game.
________________________________________
**Best Practices & Insights**

•	Use **Fargate** for reducing infrastructure maintenance.

•	Use **Ingress + ALB** instead of creating many LoadBalancer services.

•	**Namespace-scoped Fargate profiles** are unique to Fargate; remember to configure them for each custom namespace.

•	Store and manage all manifests and configurations in a GitHub repository for version control and reproducibility.

•	Use **Helm charts** for reusable and scalable deployments.

•	Integrate with IAM Roles for Service Accounts (IRSA) to securely grant AWS service access to pods.
________________________________________
**Conclusion**

EKS simplifies Kubernetes deployment on AWS, especially when paired with Fargate and ALB Ingress Controller. By following this guide, you’ve learned how to:

•	Set up a managed Kubernetes cluster using EKS

•	Deploy a real-world application

•	Expose it securely using Ingress and ALB

This setup reflects modern DevOps practices and is valuable for your portfolio, open-source contributions, and resume.
