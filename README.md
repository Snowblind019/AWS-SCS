# AWS Certified Security Specialty (SCS-C02) Notes

Welcome to my AWS SCS notes repository.

Here, I document my progress and hands-on work as I prepare for the **AWS Certified Security Specialty (SCS-C02)** certification.

I am currently following **Adrian Cantrill’s AWS SCS course**, performing practical labs and configurations in real AWS accounts to build strong hands-on skills alongside theoretical knowledge.

---

## Current Progress

### ✅ Module 1 – Course Fundamentals and AWS Account Setup

- Established a secure and organized AWS multi-account structure with Management and Production accounts.
- Enabled IAM user and role access to billing information for Admin IAM users.
- Configured MFA on root and IAM admin users for both accounts.
- Set up billing preferences to receive PDF invoices, AWS Free Tier alerts, and CloudWatch billing alerts.
- Created monthly budgets with email notifications to monitor costs proactively.
- Created account aliases and IAM users with AdministratorAccess policies to eliminate root usage for daily operations.
- Practiced security access key management for CLI use cases.
- Installed and configured the AWS CLI on Linux with named profiles for both accounts to enable streamlined multi-account management.

### ✅ Module 2 – Management and Security Governance

- Created an AWS Organization with Management as the root account.
- Invited the Production account to join and configured role delegation using OrganizationAccountAccessRole.
- Tested Switch Role functionality from Management to Production.
- Created a Development account after increasing the account quota through a service request.
- Set up organizational units (OUs) for Production and Development and moved accounts accordingly.
- Tested Service Control Policies (SCPs) by restricting S3 actions in the Production OU and validated enforcement.

### ✅ Module 3 – Domain 4: Identity and Access Management

- Created an IAM user permission boundary policy (`a4luserboundary`) restricting created users to **S3**, **CloudWatch**, and **EC2** services, while allowing them to manage their own credentials.
- Created an IAM admin delegate boundary policy (`a4ladminboundary`) allowing delegated administration only for users with a specific permission boundary, while preventing changes to the boundary policies themselves.
- Created an IAM admin permission policy (`a4ladminpermissionpolicy`) granting full IAM permissions and limited CloudWatch access.
- Created a user **bob**, attached the admin permission policy, and enforced the admin boundary to restrict actions.
- Deployed a CloudFormation template (`WEBIDF`) creating:
  - Two S3 buckets (public and private) with images.
  - A CloudFront distribution (`d1r78yms5ewf7x.cloudfront.net`).
- Created a Google OAuth 2.0 Web Client for **PETIDFServerlessApp** and configured it for Cognito integration.
- Created a Cognito Identity Pool with Google as an authentication provider, an associated IAM role (`PETIDFIDPoolAuth_Role`), and attached a policy allowing access to the private S3 bucket.
- Updated public S3 website files (`index.htm`, `scripts.js`) with correct IDs to enable Google login for private content access.
- Verified Google SSO login via CloudFront and private bucket access.
- Enabled AWS IAM Identity Center (SSO), customized the access portal URL, and created permission sets for Admin, PowerUser, ViewOnly, and Billing with 4-hour sessions.
- Created **Sally Jenkins** in the Billing group with Organization-wide billing permissions, tested, then expanded her access to PowerUser for validation.
- Created an **iamadmin** user in the `A4L-ADMINS` group with MFA enabled.
- Demonstrated **S3 pre-signed URLs** via AWS CLI.
- Tested IAM deny policies by applying and removing an inline `s3:*` deny policy to validate access restrictions.
- Experimented with **cross-account S3 access** via ACLs and bucket policies, and tested **object ownership settings** to resolve ownership conflicts.
- Created a cross-account role in Production and validated access from Management using **Switch Role**.
- Deployed an EC2 Instance Metadata lab via CloudFormation and explored metadata retrieval using:
  - `curl` commands to `/latest/meta-data/`
  - `ec2-metadata` script for detailed instance data.

### 🔄 Module 4 – [To Be Documented]

- Notes and labs for this module will be added as I progress.

### 🔄 Module 5 – [To Be Documented]

- Notes and labs for this module will be added as I progress.

### 🔄 Module 6 – [To Be Documented]

- Notes and labs for this module will be added as I progress.

### 🔄 Module 7 – [To Be Documented]

- Notes and labs for this module will be added as I progress.


---

## About Me

From the start of my career, I’ve been driven by a commitment to learning and growth. I earned my **AAS in Network Design and Administration by age 18**, started as a **NOC Analyst I**, and was promoted to **NOC Analyst II within three months** due to my initiative, adaptability, and problem-solving skills.

I have hands-on experience in network operations, working with **Cisco, Juniper, and Nokia devices**, and troubleshooting complex **MPLS, DWDM, and fiber optic networks**. I’ve also expanded my expertise into cloud technologies and security, completing comprehensive studies in **AWS architecture and security** to build a strong cloud foundation.

To strengthen my cloud security knowledge, I earned **CompTIA Network+, Security+, and CySA+**, and I am currently pursuing the **AWS Security Specialty (SCS-C02)** and **CCSP certifications**.

I thrive in roles where I can **analyze risks, design secure cloud architectures, and ensure compliance with industry standards**. With cloud security constantly evolving, I am committed to continuous learning to stay ahead of emerging threats. I’m excited to bring my dedication, adaptability, and security mindset to a team where I can develop into a **Cloud Security Engineer**, protecting critical systems and data while expanding my expertise in the field.

---

## Connect with Me

- **LinkedIn:** [https://www.linkedin.com/in/emilp-profile/](https://www.linkedin.com/in/emilp-profile/)

---

