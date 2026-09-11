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
- Does Amazon S3 require a VPC?
- What happen when we run terraform init ?
- write a terraform script to create an ec2 instance in multiple region.
- You have defined a multi-region Terraform configuration (region1, region2, region3). If you create an EC2 instance, in which region will it be deployed?
- If the frontend, backend, and database are all deployed in private subnets, how can an end user access the application?
- If secrets are created in AWS Secrets Manager, how can Amazon EKS access those secrets?
- How do you set up RBAC in Amazon EKS?
