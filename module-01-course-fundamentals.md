# Module 1 – Course Fundamentals and AWS Account Setup

## Objective

Establish a secure and organized AWS multi-account structure with proper administrative access, MFA, billing alerts, and CLI configuration to follow security best practices from the start.

## What I Did

### Management Account Setup

- Created the Management AWS root account.
- Enabled IAM user and role access to billing information so that Admin IAM users can view billing data without needing root access.
- Set up MFA for the Management root account using an authenticator app to improve account security.
- Updated billing preferences to:
  - Receive PDF invoices by email.
  - Enable AWS Free Tier usage alerts.
  - Enable CloudWatch billing alerts for proactive cost monitoring.
- Created a monthly cost budget of $10 using AWS Budgets with email notifications if thresholds are exceeded.

### Production Account Setup

- Created a separate Production AWS root account.
- Repeated all Management account security and billing configurations to ensure consistency and security compliance.

### IAM User and Role Management

- Created account aliases for both Management and Production accounts for easier console login.
- Created an IAM user named `iamadmin` in each account with the AdministratorAccess policy attached, following best practices to avoid using root accounts for daily operations.
- Logged in as the `iamadmin` user to confirm permissions and reduce dependency on the root account.
- Configured MFA for each `iamadmin` user to enforce strong authentication.

### AWS CLI Configuration

- Generated security access keys for CLI usage in both accounts.
- Practiced key management by creating multiple keys, activating, deactivating, and deleting them to understand operational workflows.
- Installed the AWS CLI on my Linux environment.
- Configured named profiles for both Management and Production accounts in the CLI for seamless multi-account management.

## Key Takeaways

- Established a secure multi-account AWS structure following least privilege principles.
- Strengthened account security by implementing MFA, proper IAM user management, and access key hygiene.
- Set up proactive billing alerts and budgets to monitor usage and control costs.
- Built a solid foundation for CLI-based automation and account management with named profiles.

## Real-World Application

These initial setups reflect enterprise-level cloud governance practices, enabling secure management, compliance alignment, and operational readiness for further AWS security and architecture configurations.
