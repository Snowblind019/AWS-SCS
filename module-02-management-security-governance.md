# Module 2 – Domain 6: Management and Security Governance

## Objective

Set up AWS Organizations to establish centralized multi-account management with proper role delegation, security controls, and service control policies.

## What I Did

### AWS Organization Setup

- Created an AWS Organization with the **Management account** as the management/root account.
- Invited the **Production account** to join the Organization using its Account ID.
- Accepted the invitation from the Production account, adding it to the Organization structure.

### IAM Role Delegation

- Created an **IAM Role** on the Production account:
  - Trusted entity: Management account.
  - Attached the **AdministratorAccess** policy.
  - Named the role `OrganizationAccountAccessRole` to allow role switching from Management to Production.
- From the Management account, performed **Switch Role** into the Production account:
  - Input Production account number and IAM role name.
  - Named it `PROD` with a red color indicator.
- Tested switching into the `PROD` role and back successfully.

### Development Account Creation

- Attempted to create a new AWS account named **Development** from the AWS Organizations console in the Management account.
- Encountered an error: *“You have exceeded the allowed number of AWS accounts.”*
  - Investigated using **Service Quotas console**.
  - Looked up **AWS Organizations quotas**, found the **Default maximum number of accounts**.
### Organizational Units (OUs)

### Service Control Policy Testing
- Created a **Service Control Policy (SCP)** for the `PROD` OU:
  - Policy allowed all actions except explicitly denied S3 actions.
  - Detached the Deny S3 policy.
  - Reattached the `FullAWSAccess` policy.
  - Emptied and deleted the S3 bucket created during testing.

## Key Takeaways

- Established centralized **multi-account management** with AWS Organizations for Production and Development environments.
- Implemented **cross-account role delegation** using `OrganizationAccountAccessRole` for secure role switching from Management.
- Learned quota management processes in AWS when encountering account creation limits.
- Practiced enforcing **Service Control Policies (SCPs)** to restrict services across Organizational Units for governance and security compliance.

## Real-World Application

These configurations reflect enterprise-level cloud governance practices, enabling centralized management, consistent security controls, and scalable account structures to support multiple environments under a single organizational framework.
  - Attached this Deny S3 policy to the `PROD` OU and detached the default `FullAWSAccess` policy.
- Verified enforcement of the SCP by testing denied S3 access.
- Reverted the changes:

- Switched role into the `PROD` role and tested permissions:
  - Created an S3 bucket, uploaded a picture, and confirmed access.
  - `DEV` OU for Development.
- Moved the Production account into the `PROD` OU.
- Moved the Development account into the `DEV` OU.

- Created two Organizational Units within the AWS Organization:
  - `PROD` OU for Production.
- Created a switch role entry in the Management account to access the Development account, similar to Production.
  - Named it `DEV` for clarity.

  - Submitted a request to AWS to increase the quota limit for account creation.
### Switch Role for Development


