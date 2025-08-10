# Module 3 – Domain 4: Identity and Access Management

Created an IAM user permission boundary JSON policy that limits any user created by a specific user to only have access to 3 services, mainly for changing their own details.

**a4luserboundary**
```json
{
  "Version": "2012-10-17",
  "Statement": [
      {
          "Sid": "ServicesLimitViaBoundaries",
          "Effect": "Allow",
          "Action": [
              "s3:*",
              "cloudwatch:*",
              "ec2:*"
          ],
          "Resource": "*"
      },
      {
          "Sid": "AllowIAMConsoleForCredentials",
          "Effect": "Allow",
          "Action": [
              "iam:ListUsers",
              "iam:GetAccountPasswordPolicy"
          ],
          "Resource": "*"
      },
      {
          "Sid": "AllowManageOwnPasswordAndAccessKeys",
          "Effect": "Allow",
          "Action": [
              "iam:*AccessKey*",
              "iam:ChangePassword",
              "iam:GetUser",
              "iam:*ServiceSpecificCredential*",
              "iam:*SigningCertificate*"
          ],
          "Resource": ["arn:aws:iam::*:user/${aws:username}"]
      }
  ]
}
```

Created an IAM admin delegate boundary JSON policy that only allows the user to perform these actions on a user that has the specific permission boundary, and not on themselves.

**a4ladminboundary**
```json
{
  "Version": "2012-10-17",
  "Statement": [
      {
          "Sid": "CreateOrChangeOnlyWithBoundary",
          "Effect": "Allow",
          "Action": [
              "iam:CreateUser",
              "iam:DeleteUserPolicy",
              "iam:AttachUserPolicy",
              "iam:DetachUserPolicy",
              "iam:PutUserPermissionsBoundary",
              "iam:PutUserPolicy"
          ],
          "Resource": "*",
          "Condition": {
              "StringEquals": {
                  "iam:PermissionsBoundary": "arn:aws:iam::731733337976:policy/a4luserboundary"
              }
          }
      },
      {
          "Sid": "CloudWatchAndOtherIAMTasks",
          "Effect": "Allow",
          "Action": [
              "cloudwatch:*",
              "iam:GetUser",
              "iam:ListUsers",
              "iam:DeleteUser",
              "iam:UpdateUser",
              "iam:CreateAccessKey",
              "iam:CreateLoginProfile",
              "iam:GetAccountPasswordPolicy",
              "iam:GetLoginProfile",
              "iam:ListGroups",
              "iam:ListGroupsForUser",
              "iam:CreateGroup",
              "iam:GetGroup",
              "iam:DeleteGroup",
              "iam:UpdateGroup",
              "iam:CreatePolicy",
              "iam:DeletePolicy",
              "iam:DeletePolicyVersion",
              "iam:GetPolicy",
              "iam:GetPolicyVersion",
              "iam:GetUserPolicy",
              "iam:GetRolePolicy",
              "iam:ListPolicies",
              "iam:ListPolicyVersions",
              "iam:ListEntitiesForPolicy",
              "iam:ListUserPolicies",
              "iam:ListAttachedUserPolicies",
              "iam:ListRolePolicies",
              "iam:ListAttachedRolePolicies",
              "iam:SetDefaultPolicyVersion",
              "iam:SimulatePrincipalPolicy",
              "iam:SimulateCustomPolicy"
          ],
          "NotResource": "arn:aws:iam::731733337976:user/bob"
      },
      {
          "Sid": "NoBoundaryPolicyEdit",
          "Effect": "Deny",
          "Action": [
              "iam:CreatePolicyVersion",
              "iam:DeletePolicy",
              "iam:DeletePolicyVersion",
              "iam:SetDefaultPolicyVersion"
          ],
          "Resource": [
              "arn:aws:iam::731733337976:policy/a4luserboundary",
              "arn:aws:iam::731733337976:policy/a4ladminboundary"
          ]
      },
      {
          "Sid": "NoBoundaryUserDelete",
          "Effect": "Deny",
          "Action": "iam:DeleteUserPermissionsBoundary",
          "Resource": "*"
      }
  ]
}
```

Created a 3rd permission policy for the user so that they have full admin privileges.

**a4ladminpermissionpolicy**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "IAM",
      "Effect": "Allow",
      "Action": "iam:*",
      "Resource": "*"
    },
    {
      "Sid": "CloudWatchLimited",
      "Effect": "Allow",
      "Action": [
        "cloudwatch:GetDashboard",
        "cloudwatch:GetMetricData",
        "cloudwatch:ListDashboards",
        "cloudwatch:GetMetricStatistics",
        "cloudwatch:ListMetrics"
      ],
      "Resource": "*"
    }
  ]
}
```

- Created user **bob** providing access to the AWS Management Console and set up a console password.  
- Attached the `a4ladminpermissionpolicy` to user bob but also set a permission boundary `a4ladminboundary` to limit what Bob can do despite having full admin rights.  

---

## CloudFormation & Google SSO Integration

- Deployed a one-click CloudFormation template with:
  - Stack name: **WEBIDF**
  - `labss3contentbucket = cl-labs-s3content`
  - `labss3contentprefix = aws-cognito-web-identity-federation/appbucket`
- Created:
  - **2 S3 buckets** (private & public)
  - **CloudFront distribution** with domain `d1r78yms5ewf7x.cloudfront.net`
- On Google Credentials:
  - Created project **PETIDF**
  - Configured OAuth consent screen (External users, OAuth 2.0)
  - Created OAuth Web App Client (**PETIDFServerlessApp**) with CloudFront URI in Authorized JS origins  
    Client ID: `805713079922-avq2pvv8s4cooorcjar0ncu7aqkt45m3.apps.googleusercontent.com`
- Created Cognito Identity Pool using Google as Auth source:
  - IAM Role: **PETIDFIDPoolAuth_Role**
  - Identity Pool ID: `us-east-1:c8b78d48-a401-4b9e-a318-bc63e313a25b`
  - Attached `PrivatePatchesPermissions` policy for S3 private bucket access
- Updated and re-uploaded `index.htm` and `scripts.js` to public S3 bucket.
- Successfully logged into CloudFront link via Google SSO and accessed private S3 bucket.

---

## AWS SSO (IAM Identity Center) Setup

- Enabled IAM Identity Center.
- Customized AWS Access Portal URL: `https://notanimals4life.awsapps.com/start`
- Created **4 Permission Sets** (AdminAccess, PowerUserAccess, ViewOnlyAccess, Billing) — all with 4-hour sessions.
- Created **Sally Jenkins** (Billing dept) → Added to **Billing Group** → Org-wide access to Billing.
- Verified account restrictions by logging in as Sally.
- Created **PowerUsers** group → Added Sally → Assigned PowerUserAccess → Verified access change.
- Created Dropbox Application for group, then deleted it along with Sally’s config.
- Created `iamadmin` user & **A4L-ADMINS** group → Assigned AdminAccess → Enabled MFA.

---

## S3 Access Controls & Testing

- Created bucket `animals4lifemedia2325` and uploaded image.
- Ran:
  ```bash
  aws s3 presign s3://animals4lifemedia2325/all5.jpg --expires-in 180
  ```
- Created **inline deny policy** for `iamadmin` to block S3 access, tested, then removed policy.

```json
{
  "Version": "2012-10-17",
  "Statement": [
      {
          "Sid": "VisualEditor0",
          "Effect": "Deny",
          "Action": "s3:*",
          "Resource": "*"
      }
  ]
}
```

---

### Cross-Account Access via ACL & Bucket Policy

- **ACL method:** Granted Management AWS account access via Canonical User ID → Uploaded file from Mgmt account → Verified Production account couldn’t access it.
- **Bucket Policy method:** Edited bucket policy, still couldn’t access until **Object Ownership** set to *Bucket Owner Preferred*.  
  Also had to expand permissions to `s3:*` for deletion.

```json
{
  "Version": "2012-10-17",
  "Statement": [
      {
          "Effect": "Allow",
          "Principal": {
              "AWS": "arn:aws:iam::731733337976:user/iamadmin"
          },
          "Action": [
              "s3:GetObject",
              "s3:PutObject",
              "s3:PutObjectAcl",
              "s3:ListBucket"
          ],
          "Resource": [
              "arn:aws:s3:::buckets-petpics2-xlbz3hgqmzmu/*",
              "arn:aws:s3:::buckets-petpics2-xlbz3hgqmzmu"
          ]
      }
  ]
}
```

---

## Role Switching

- Created new role for Production account with Account ID.
- Switched role from Management to Production.
- Uploaded files in S3 bucket:
  - No access from Management account.
  - Full access via Production account or switched role.

---

## EC2 Instance Metadata Lab

- Deployed CloudFormation Instance Metadata environment.
- Connected via public IPv4 and tested metadata commands:
  ```bash
  curl http://169.254.169.254/latest/meta-data/public-ipv4
  curl http://169.254.169.254/latest/meta-data/public-hostname

  wget http://s3.amazonaws.com/ec2metadata/ec2-metadata
  chmod u+x ec2-metadata

  ec2-metadata --help
  ec2-metadata -a
  ec2-metadata -z
  ec2-metadata -s
  ```
