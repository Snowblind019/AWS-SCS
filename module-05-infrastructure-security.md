# Module 5 – Domain 3: Infrastructure Security

## VPC and Subnet Setup

- Created a VPC `a4l-vpc1` with IPv4 CIDR `10.16.0.0/16` and an Amazon-provided IPv6 CIDR block.
- Enabled **DNS resolution** and **DNS hostnames** in VPC settings.
- Created **12 subnets** (4 per AZ), manually assigned IPv4 and IPv6 CIDRs, and enabled **auto-assign IPv6 addresses**.

| Subnet Name     | IPv4 CIDR        | AZ  | Custom IPv6 Suffix |
|------------------|------------------|-----|---------------------|
| sn-reserved-A    | 10.16.0.0/20     | AZA | 00                  |
| sn-db-A          | 10.16.16.0/20    | AZA | 01                  |
| sn-app-A         | 10.16.32.0/20    | AZA | 02                  |
| sn-web-A         | 10.16.48.0/20    | AZA | 03                  |
| sn-reserved-B    | 10.16.64.0/20    | AZB | 04                  |
| sn-db-B          | 10.16.80.0/20    | AZB | 05                  |
| sn-app-B         | 10.16.96.0/20    | AZB | 06                  |
| sn-web-B         | 10.16.112.0/20   | AZB | 07                  |
| sn-reserved-C    | 10.16.128.0/20   | AZC | 08                  |
| sn-db-C          | 10.16.144.0/20   | AZC | 09                  |
| sn-app-C         | 10.16.160.0/20   | AZC | 0A                  |
| sn-web-C         | 10.16.176.0/20   | AZC | 0B                  |

- Created **Internet Gateway** `a4l-vpc1-igw`, attached to VPC.
- Created **route table** `a4l-vpc1-rt-web`, added IPv4 and IPv6 default routes.
- Associated route table with all 3 web subnets.
- Enabled auto-assign **public IPv4 addresses** on web subnets.
- Launched EC2 instance `a4l-bastion` into `sn-web-A` with public and IPv6 addressing. Created security group `A4l-BASTION-SG`.
- After testing, deleted the instance and manually created infrastructure.

## CloudFormation VPC Automation

- Used CloudFormation template to **automate** full VPC setup.
- Created **three NAT Gateways** (in `sn-web-A/B/C`) with Elastic IPs.
- Created **three route tables**: `a4l-vpc1-rt-privateA`, `-privateB`, `-privateC`.
- Configured default IPv4 routes to NAT Gateways.
- Associated each private subnet to the correct route table.
- Verified NAT traffic, then deleted all resources.

---

## Site-to-Site VPN with pfSense+

- Subscribed to Netgate pfSense+ AMI from Marketplace.
- Created key pair `infra`, launched EC2 using CloudFormation template.
- Created:
  - **Customer Gateway**: IP `98.87.56.10`
  - **Virtual Private Gateway (VGW)**, attached to A4L-AWS VPC
- Created **VPN connection** with static routing to `192.168.8.0/21`.
- Configured pfSense:
  - **LAN interface** with DHCP
  - Two IPSec tunnels with AWS:
    - **Tunnel 1:**
      - IKEv1, AES128, SHA1, DH2, PSK
      - Phase 2: ESP AES128, SHA1-96, PFS2
    - **Tunnel 2:** Same parameters, separate PSK
- Enabled **route propagation** in AWS route tables.
- Added on-prem routes and security group rules.
- Verified cross-site communication via ping and HTTP.
- Cleaned up all VPN and EC2 resources.

---

## VPC Endpoints (Private Access)

- Created **EC2 Instance Connect Endpoint**, verified private EC2 SSH access via console.
- Created **S3 Gateway VPC Endpoint**, attached to route table.

### S3 Endpoint Policy

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::a4lpriv-privatecats-o1hcncyadrtp/*",
        "arn:aws:s3:::a4lpriv-publiccats-7pbb1wtidy2i/*"
      ]
    },
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:ListBucket",
      "Resource": [
        "arn:aws:s3:::a4lpriv-privatecats-o1hcncyadrtp",
        "arn:aws:s3:::a4lpriv-publiccats-7pbb1wtidy2i"
      ]
    },
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": [
        "s3:ListAllMyBuckets",
        "s3:GetBucketLocation"
      ],
      "Resource": "*"
    }
  ]
}
```

- Verified public internet access was blocked using **Deny condition** policy based on source IP or VPC endpoint presence.

---

## VPC Peering Demo

- Used CloudFormation Stack `A4LPEERING` to create VPC peering connections:
  - A ↔ B
  - B ↔ C
  - A ↔ C
- Accepted connections and configured:
  - Route tables in all 3 VPCs
  - SGs to allow ICMP
- Verified communication via **ping** across VPCs.

---

## EBS Hands-On

- Created **gp3 EBS volume** (10 GB), attached to EC2 1-A at `/dev/sdf`.
- Mounted as XFS, added to `/etc/fstab`, wrote file to disk.
- Detached volume, attached to EC2 2-A, verified data.
- Created **snapshot**, launched in AZ 1-B, mounted volume from snapshot, confirmed persistence.

---

## Instance Store Demo

- Launched **m5dn.large** with instance store at `/dev/nvme1n1`.
- Mounted as XFS, wrote file.
- Rebooted: file persisted.
- Stopped/started instance: file was **lost** — confirms ephemeral behavior.

---

## Key Takeaways

- **IPv6-enabled subnets** future-proof large-scale networks with scalable, unique addressing.
- **NAT Gateways** isolate private subnets while enabling controlled internet access.
- **VPNs** are the backbone of hybrid architectures and require deep config awareness (IKE/ESP phases, SGs, routes).
- **VPC Endpoints** allow secure, private access to AWS services — critical for regulated environments.
- **Peering connections** must be manually routed and security-group opened; not transitive.
- **EBS volumes** are portable and persistent — ideal for applications needing reliability.
- **Instance Store** is high-speed but ephemeral — best for temp workloads or caching layers.

---

## Real-World Application

- **Enterprise Network Design**: Mirroring this level of subnetting and dual-stack support is foundational for large orgs with hybrid or global deployment needs.
- **NAT Gateway Best Practices**: Used widely in multi-tier VPCs to isolate app and DB layers.
- **IPSec Tunnels**: Direct application for on-prem to AWS secure connectivity for migrations or DR setups.
- **Private Endpoints**: Needed for compliance standards (PCI-DSS, HIPAA, FedRAMP) to avoid public internet exposure.
- **Peering Topologies**: Enables cross-team, cross-account integrations in microservice architectures.
- **EBS Use Cases**: Backing storage for RDS, file servers, or self-managed DBs.
- **Instance Store**: Often used in ML training jobs or ephemeral containers with high IOPS needs.

