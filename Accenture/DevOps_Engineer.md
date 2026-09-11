# Accenture

**EXP- 6yrs**


- You have created an IAM user in AWS and configured role-based access in EKS. How do you bind the IAM user to the EKS role?
Quick Answer
---------------------------------------------------------------
You do not directly bind an IAM user to a Kubernetes Role. Instead:

Map the IAM user (or IAM role) to a Kubernetes user/group using aws-auth or EKS Access Entries.
Create a Kubernetes Role/ClusterRole.
Create a RoleBinding/ClusterRoleBinding that binds the Kubernetes group/user to that role.

That mapping layer (IAM → Kubernetes user/group) is the key concept in EKS authentication and authorization
--------------------------------------------------------------
- Assume you have 10 AWS accounts. How will you securely log in to them, considering access keys are not used for security reasons?
- --------------------------------------------------------------------------------------------------------
**Traditional Approach (Not Recommended)**
Create IAM users in every account.
Dev Account → naveen
Test Account → naveen
Prod Account → naveen
**Problems:**
Password management
User duplication
Access key rotation
Difficult auditing
10 accounts × 50 users = 500 IAM users
This is not scalable.

**Recommended Approach: IAM Identity Center (AWS SSO)** Create a single identity.
naveen@company.com inside:AWS IAM Identity Center
User authenticates once using:Username + Password + MFA
Step 1: Enable IAM Identity Center

In the management account:AWS Organizations
        ↓
IAM Identity Center
Enable:Single Sign-On
Step 2: Create Users naveen@company.com or integrate with: AD
Step 3: Create Permission Sets
Example:Developer Permission Set
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "eks:*",
        "ec2:Describe*",
        "s3:Get*"
      ],
      "Resource": "*"
    }
  ]
}

Step 4: Assign Access to accounts Dev Acc: Developer access, test Acc: Developer access, Prod Acc: ReadOnly access
**Interview Answer (Short)**

In a multi-account AWS environment, I would use AWS Organizations with IAM Identity Center (AWS SSO). Users authenticate once using SSO and MFA. Access is granted through Permission Sets, which create IAM roles in target accounts. When a user selects an account, AWS uses STS AssumeRole to provide temporary credentials instead of access keys. For EKS access, the SSO-generated IAM role is mapped to Kubernetes RBAC using EKS Access Entries or aws-auth mappings. This provides centralized access management, strong security, and easy auditing across all AWS accounts.

----------------------------------------------------------------------------------------------------------
- What are the ways to log in to an AWS account?
- --------------------------------------------------------------------------------------------------------
Interview Answer
AWS accounts can be accessed using several methods: Root User, IAM Users, IAM Identity Center (AWS SSO), AssumeRole, Federated Login through Azure AD or Okta, and programmatic access using IAM Roles. In modern enterprises, the preferred method is AWS IAM Identity Center integrated with an Identity Provider and MFA. Users authenticate through SSO and assume IAM roles in AWS accounts using temporary credentials instead of long-lived access keys. This provides centralized access management and better security.

----------------------------------------------------------------------------------------------------------
- Does Amazon S3 require a VPC?
- --------------------------------------------------------------------------------------------------------
Interview Answer

No, Amazon S3 does not require a VPC because S3 is a regional AWS-managed service that exists outside of customer VPCs. You can create and use S3 buckets without having any VPC in your account. However, resources inside a VPC, such as EC2 instances or EKS workloads, can access S3 either through public S3 endpoints using the internet or, more securely, through an S3 VPC Endpoint, which keeps traffic within the AWS network and avoids the need for an Internet Gateway or NAT Gateway.

Interview Question Twist
Q: Is S3 deployed inside a VPC?
Answer: No.
VPC Resources
-------------
EC2
RDS
EKS Nodes
ALB/NLB
Lambda ENIs

Outside VPC
-----------
S3
DynamoDB
IAM
CloudFront
Route53

-----------------------------------------------------------------------------------------------------------
- What happen when we run terraform init ?
- ---------------------------------------------------------------------------------------------------------
When we run terraform init, Terraform initializes the working directory. It downloads required provider plugins, initializes and configures the backend for state management, downloads referenced modules, creates the .terraform directory, and generates the terraform.lock.hcl file. It does not create or modify infrastructure; it only prepares the environment so that terraform plan and terraform apply can be executed successfully.

Think of it like:
terraform init
      ↓
Download Providers
      ↓
Configure Backend
      ↓
Download Modules
      ↓
Create .terraform Directory
      ↓
Ready for Plan & Apply

-----------------------------------------------------------------------------------------------------------
- write a terraform script to create an ec2 instance in multiple region.
- ---------------------------------------------------------------------------------------------------------
If you want to create EC2 instances in multiple AWS regions using Terraform, the best approach is to use multiple provider configurations (provider aliases) and create resources in each region.

Example: Create EC2 Instances in Mumbai and Virginia
**providers.tf**
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

# Mumbai
provider "aws" {
  alias  = "mumbai"
  region = "ap-south-1"
}

# Virginia
provider "aws" {
  alias  = "virginia"
  region = "us-east-1"
}

**main.tf**
# EC2 in Mumbai
resource "aws_instance" "mumbai_ec2" {
  provider      = aws.mumbai
  ami           = "ami-0f58b397bc5c1f2e8" # Example Amazon Linux 2023 AMI
  instance_type = "t2.micro"

  tags = {
    Name = "Mumbai-EC2"
  }
}

# EC2 in Virginia
resource "aws_instance" "virginia_ec2" {
  provider      = aws.virginia
  ami           = "ami-0c101f26f147fa7fd" # Example Amazon Linux 2023 AMI
  instance_type = "t2.micro"

  tags = {
    Name = "Virginia-EC2"
  }
}

-----------------------------------------------------------------------------------------------------------
- You have defined a multi-region Terraform configuration (region1, region2, region3). If you create an EC2 instance, in which region will it be deployed?
- ----------------------------------------------------------------------------------------------------------
Case 1: Multiple Providers Defined
provider "aws" {
  alias  = "mumbai"
  region = "ap-south-1"
}

provider "aws" {
  alias  = "virginia"
  region = "us-east-1"
}

provider "aws" {
  alias  = "london"
  region = "eu-west-2"
}
**If you create a resource without specifying a provider:**
resource "aws_instance" "web" {
  ami           = "ami-xxxx"
  instance_type = "t2.micro"
}
Terraform will throw an error unless one provider is defined as the default provider.
**Interview Answer**
Simply defining multiple regions in Terraform does not automatically deploy resources to all regions. An EC2 instance is created in the region associated with the provider used by that resource. If a resource uses provider = aws.region1, it is deployed in Region1. If no provider is specified, Terraform uses the default provider. To deploy instances across multiple regions, separate resources or modules must explicitly reference the respective provider aliases.

------------------------------------------------------------------------------------------------------------
- If the frontend, backend, and database are all deployed in private subnets, how can an end user access the application?
---------------------------------------------------------------------------------------------------------------
Short Answer

If frontend, backend, and database are all in private subnets, users cannot directly access them from the internet.
You need a public entry point, typically:
Internet-facing Load Balancer (ALB/NLB)
CloudFront
API Gateway
The backend and database remain private.

Typical Architecture
Internet User
      |
      v
Public ALB
      |
      v
Frontend (Private Subnet)
      |
      v
Backend (Private Subnet)
      |
      v
Database (Private Subnet)
---------------------------------------------------------------------------------------------------------------
- If secrets are created in AWS Secrets Manager, how can Amazon EKS access those secrets?
- --------------------------------------------------------------------------------------------------------------
Interview Answer
Secrets stored in AWS Secrets Manager are typically accessed from EKS using IAM Roles for Service Accounts (IRSA). A Kubernetes ServiceAccount is mapped to an IAM role that has permission to read the secret. The application can then retrieve the secret using either the AWS Secrets Store CSI Driver, which mounts the secret directly into the pod, or the External Secrets Operator, which synchronizes Secrets Manager secrets into Kubernetes Secrets. This eliminates the need for hardcoded credentials and provides secure, fine-grained access control.
Step 1: Store Secret in AWS Secrets Manager
Step 2: Create IAM Policy
Step 3: Create IAM Role for Service Account (IRSA)
Step 4: Create Kubernetes ServiceAccount

AWS Secrets Manager
        │
        ▼
IAM Role (IRSA)
        │
        ▼
Secrets Store CSI Driver
        │
        ▼
EKS Pod

----------------------------------------------------------------------------------------------------------------
- How do you set up RBAC in Amazon EKS?
----------------------------------------------------------------------------------------------------------------
Real-Life Example

Imagine a company has:

EKS Cluster = Office Building
IAM User = Employee (Naveen)
Role = Permission Card

You don't want every employee to have admin access.

You want:
Naveen → Can only view pods
Admin   → Can do everything

This is what RBAC does.

**Step 1: Create IAM User in AWS**
IAM User: naveen-user
AWS now knows who Naveen is. But EKS still doesn't know what Naveen can do.
**Step 2: Tell EKS About This User**
Map the IAM user to a Kubernetes group.
**Example:**
mapUsers:
  - userarn: arn:aws:iam::123456789012:user/naveen-user
    username: naveen
    groups:
      - developers
   
**Meaning:**
AWS IAM User
    ↓
naveen-user
    ↓
Kubernetes Group
    ↓
developers
**Step 3: Create a Role**
Let's allow viewing pods only.
kind: Role
metadata:
  name: pod-reader
  namespace: default

rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
**Meaning**
Can see pods
Cannot create pods
Cannot delete pods
**Step 4: Bind Group to Role**
Create a RoleBinding.
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: default

subjects:
- kind: Group
  name: developers

roleRef:
  kind: Role
  name: pod-reader
**Meaning**
developers group
       ↓
gets
       ↓
pod-reader role

**Final Flow**
IAM User
(naveen-user)
      ↓
Mapped to Group
(developers)
      ↓
RoleBinding
      ↓
Role
(pod-reader)
      ↓
Permission
(List Pods)

Simple Interview Answer

RBAC in EKS is configured by mapping an IAM user or IAM role to a Kubernetes user/group using aws-auth or EKS Access Entries. Then a Kubernetes Role or ClusterRole is created with the required permissions, and a RoleBinding or ClusterRoleBinding is used to bind the user/group to that role. For example, I can map an IAM user to a developers group and bind that group to a role that allows only get and list operations on pods. This gives the user read-only access to the cluster resources.
