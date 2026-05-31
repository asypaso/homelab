```python
```markdown
# Project 4: Cloud Infrastructure & Security (AWS)

## Objective

Deploy and secure cloud infrastructure on AWS Free Tier — building virtual machines, configuring networking and security groups, managing identity and access (IAM), and implementing cloud monitoring. This demonstrates the ability to work in hybrid environments that combine on-premises (home lab) with cloud resources.

## Environment

| Component | Details |
|-----------|---------|
| Cloud Provider | AWS (Free Tier) |
| Region | us-east-1 (N. Virginia) — or your closest region |
| Services Used | EC2, VPC, IAM, CloudWatch, S3, Security Groups |
| Local Lab | Phases 1-3 home lab (VirtualBox) |
| Budget | $0 (Free Tier only) |

## Architecture Diagram



## What This Project Covers

| Skill Area | What You'll Do |
|------------|---------------|
| Cloud Networking | Build a VPC with public/private subnets |
| Compute | Launch and configure EC2 instances |
| Security | Configure Security Groups (cloud firewalls) |
| Identity | Create IAM users, groups, roles, and policies |
| Monitoring | Set up CloudWatch alarms and dashboards |
| Storage | Create and secure S3 buckets |
| Cost Management | Stay within Free Tier limits |

---

## Step 1: Create AWS Account

**Date completed:** ___________

### What I did:
- Created AWS Free Tier account at aws.amazon.com/free
- Enabled MFA on root account
- Set up billing alerts to avoid unexpected charges

### Screenshot:
!AWS Console (copy and paste the URL into your browser): screenshots/01-aws-console.png

### Free Tier limits to remember:

| Service | Free Tier Limit |
|---------|----------------|
| EC2 | 750 hours/month of t2.micro (Linux or Windows) |
| S3 | 5 GB storage, 20,000 GET requests |
| CloudWatch | 10 custom metrics, 10 alarms |
| VPC | Free (no charge for VPC itself) |
| Data Transfer | 100 GB out per month |

### Notes:


---

## Step 2: Secure the Root Account

**Date completed:** ___________

### What I did:
- Enabled Multi-Factor Authentication (MFA) on root account
- Created an admin IAM user (never use root for daily work)
- Applied password policy

### Security checklist:

| Task | Completed |
|------|-----------|
| MFA enabled on root | |
| Admin IAM user created | |
| Root access keys deleted (if any) | |
| Password policy configured | |
| Billing alert set ($5 threshold) | |

### Screenshot:
!MFA Enabled (copy and paste the URL into your browser): screenshots/02-mfa-enabled.png

### Notes:


---

## Step 3: Create IAM Users, Groups, and Policies

**Date completed:** ___________

### What I did:
- Created IAM groups with appropriate permissions:

| Group Name | Policy Attached | Purpose |
|------------|----------------|---------|
| Admins | AdministratorAccess | Full access (your admin user) |
| Developers | PowerUserAccess | Can create resources, no IAM changes |
| ReadOnly | ReadOnlyAccess | View only — no changes allowed |
| S3Managers | AmazonS3FullAccess | Manage S3 buckets only |

- Created IAM users:

| User | Group | MFA | Purpose |
|------|-------|-----|---------|
| anderson-admin | Admins | ✅ | Your daily admin account |
| dev-user | Developers | ✅ | Simulated developer |
| audit-user | ReadOnly | ✅ | Simulated auditor |
| s3-service | S3Managers | ❌ | Service account for S3 |

### Screenshots:
!IAM Groups (copy and paste the URL into your browser): screenshots/03-iam-groups.png

!IAM Users (copy and paste the URL into your browser): screenshots/04-iam-users.png

### Notes:


---

## Step 4: Create a Custom IAM Policy

**Date completed:** ___________

### What I did:
- Created a custom policy that allows EC2 actions only in us-east-1 region
- Attached to Developers group (restricts where they can launch instances)

### Custom policy JSON:
```json
Policy content here

Instance ID:
Public IP:
Private IP:
Status: Running

ssh -i "anderson-lab-key.pem" ec2-user@[PUBLIC_IP]

whoami
# ec2-user

hostname
# WebServer-01

curl ifconfig.me
# [shows public IP]

ping 8.8.8.8
# Reply from Google DNS

sudo yum update -y
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
echo "<h1>Anderson's Cloud Lab - Web Server Running!</h1>" | sudo tee /var/www/html/index.html

# Can reach Database-01 on port 3306?
nc -zv [IP_ADDRESS] 3306
# Expected: Connection succeeded ✅

# Can reach internet?
ping 8.8.8.8
# Expected: Reply ✅

# Can reach internet (through NAT Gateway)?
ping 8.8.8.8
# Expected: Reply ✅ (for updates)

# Has NO public IP — can't be reached directly from internet
# Expected: No public IP assigned ✅

# Can reach WebServer-01 on port 80?
[PUBLIC_IP]
# Expected: Web page loads ✅

# Can reach Database-01 directly?
# Expected: IMPOSSIBLE ❌ (no public IP, no inbound rules from internet)

ssh -i key.pem ec2-user@IP
aws iam list-users
aws ec2 describe-instances
aws s3 ls
aws cloudwatch describe-alarms
sudo yum update -y
sudo systemctl status httpd
curl ifconfig.me
