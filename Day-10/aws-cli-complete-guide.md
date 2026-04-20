# Day 10 - AWS CLI Complete Guide

## Date: 20 April 2026

---

## AWS CLI কেন দরকার এবং Main কাজ কী?

**বাংলা:** AWS CLI মানে Command Line Interface। সাধারণত AWS Console এ গিয়ে click করে কাজ করি। কিন্তু CLI দিয়ে terminal এ command লিখে একই কাজ করা যায়। Developer দের জন্য এইটা অনেক দ্রুত, powerful আর automation এর জন্য দরকারি।

**English:** AWS CLI (Command Line Interface) is a unified tool to manage AWS services from the command line. Instead of clicking through the AWS Console, you can control multiple AWS services with commands and automate them through scripts.

**AWS CLI এর Main ৪টা কাজ:**

| কাজ | উদাহরণ |
|-----|--------|
| AWS resources manage করা | EC2 start/stop করা |
| Automation | Script দিয়ে কাজ করা |
| Fast deployment | একটা command এ deploy |
| CI/CD pipeline | Auto deployment |

**Real life:** AWS Console এ ১০টা click করতে হয় — CLI তে একটা command!

---

## 1. What is AWS CLI?

**বাংলা:** AWS CLI হলো একটা tool যেটা দিয়ে terminal থেকে AWS services control করা যায়। Python দিয়ে বানানো। Windows, Mac, Linux সব জায়গায় কাজ করে।

**English:** The AWS Command Line Interface (AWS CLI) is an open source tool that enables you to interact with AWS services using commands in your command-line shell. With minimal configuration, the AWS CLI enables you to start running commands that implement functionality equivalent to that provided by the browser-based AWS Management Console.

---

## 2. How to Install AWS CLI?

**বাংলা:** AWS CLI install করতে হয় তোমার computer এ।

**English:** AWS CLI can be installed on Windows, macOS, and Linux.

**Windows:**
```bash
# Download করো এই link থেকে:
# https://awscli.amazonaws.com/AWSCLIV2.msi
# Install করো
# Verify করো:
aws --version
```

**Mac:**
```bash
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /
aws --version
```

**Linux:**
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

---

## 3. How to Configure AWS CLI?

**বাংলা:** AWS CLI configure করতে Access Key আর Secret Key লাগে। IAM থেকে এই keys বানানো হয়।

**English:** After installing, you need to configure the AWS CLI with your credentials.

**Command:**
```bash
aws configure
```

**যা জিজ্ঞেস করবে:**
AWS Access Key ID: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name: ap-southeast-1
Default output format: json

**Access Key কোথায় পাবে:**
- IAM → Users → তোমার user → Security credentials
- Create access key → Download করো

---

## 4. What are AWS CLI Basic Commands?

**বাংলা:** AWS CLI এর commands এর structure হলো:
aws [service] [action] [options]

**English:** AWS CLI commands follow a consistent structure.

**Structure:**
```bash
aws <service> <command> [options]

# উদাহরণ:
aws s3 ls                    # S3 buckets list করো
aws ec2 describe-instances   # EC2 instances দেখো
aws iam list-users           # IAM users দেখো
```

---

## 5. AWS CLI S3 Commands

**বাংলা:** S3 এর জন্য সবচেয়ে বেশি use হওয়া commands।

**English:** Common S3 commands using AWS CLI.

```bash
# Buckets list করো
aws s3 ls

# Specific bucket এর contents দেখো
aws s3 ls s3://my-bucket

# Bucket বানাও
aws s3 mb s3://my-new-bucket

# File upload করো
aws s3 cp file.txt s3://my-bucket/

# File download করো
aws s3 cp s3://my-bucket/file.txt ./

# Folder sync করো
aws s3 sync ./local-folder s3://my-bucket/

# File delete করো
aws s3 rm s3://my-bucket/file.txt

# Bucket delete করো (empty হতে হবে)
aws s3 rb s3://my-bucket

# Bucket force delete করো (সব files সহ)
aws s3 rb s3://my-bucket --force
```

---

## 6. AWS CLI EC2 Commands

**বাংলা:** EC2 এর জন্য সবচেয়ে বেশি use হওয়া commands।

**English:** Common EC2 commands using AWS CLI.

```bash
# Instances দেখো
aws ec2 describe-instances

# Running instances দেখো
aws ec2 describe-instances --filters "Name=instance-state-name,Values=running"

# Instance start করো
aws ec2 start-instances --instance-ids i-1234567890abcdef0

# Instance stop করো
aws ec2 stop-instances --instance-ids i-1234567890abcdef0

# Instance terminate করো
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0

# Instance launch করো
aws ec2 run-instances \
  --image-id ami-0abcdef1234567890 \
  --instance-type t2.micro \
  --key-name my-key-pair \
  --security-group-ids sg-12345678

# Key pairs দেখো
aws ec2 describe-key-pairs

# Security groups দেখো
aws ec2 describe-security-groups

# AMIs দেখো
aws ec2 describe-images --owners self
```

---

## 7. AWS CLI IAM Commands

**বাংলা:** IAM এর জন্য সবচেয়ে বেশি use হওয়া commands।

**English:** Common IAM commands using AWS CLI.

```bash
# Users list করো
aws iam list-users

# User বানাও
aws iam create-user --user-name salim-dev

# User delete করো
aws iam delete-user --user-name salim-dev

# Groups list করো
aws iam list-groups

# Group বানাও
aws iam create-group --group-name developer-group

# User কে group এ add করো
aws iam add-user-to-group \
  --user-name salim-dev \
  --group-name developer-group

# Roles list করো
aws iam list-roles

# Policies list করো
aws iam list-policies --scope Local
```

---

## 8. AWS CLI VPC Commands

**বাংলা:** VPC এর জন্য সবচেয়ে বেশি use হওয়া commands।

**English:** Common VPC commands using AWS CLI.

```bash
# VPCs দেখো
aws ec2 describe-vpcs

# VPC বানাও
aws ec2 create-vpc --cidr-block 10.0.0.0/16

# Subnets দেখো
aws ec2 describe-subnets

# Subnet বানাও
aws ec2 create-subnet \
  --vpc-id vpc-12345678 \
  --cidr-block 10.0.1.0/24

# Internet Gateway দেখো
aws ec2 describe-internet-gateways

# Internet Gateway বানাও
aws ec2 create-internet-gateway

# Internet Gateway attach করো
aws ec2 attach-internet-gateway \
  --internet-gateway-id igw-12345678 \
  --vpc-id vpc-12345678

# Route tables দেখো
aws ec2 describe-route-tables

# Security groups দেখো
aws ec2 describe-security-groups
```

---

## 9. AWS CLI Output Formats

**বাংলা:** AWS CLI output বিভিন্ন format এ দেখা যায়।

**English:** AWS CLI supports different output formats.

```bash
# JSON format (default)
aws s3 ls --output json

# Table format (সহজে পড়া যায়)
aws s3 ls --output table

# Text format
aws s3 ls --output text

# YAML format
aws s3 ls --output yaml
```

**Default format set করো:**
```bash
aws configure set output table
```

---

## 10. AWS CLI Profiles

**বাংলা:** একাধিক AWS account manage করতে profiles use করা হয়।

**English:** AWS CLI profiles allow you to manage multiple AWS accounts or roles.

```bash
# নতুন profile বানাও
aws configure --profile production

# Specific profile use করো
aws s3 ls --profile production

# Default profile change করো
export AWS_PROFILE=production

# Profiles দেখো
aws configure list-profiles
```

---

## 11. AWS CLI Useful Tips

**বাংলা:** AWS CLI আরও effectively use করার tips।

**English:** Tips to use AWS CLI more effectively.

**Query দিয়ে specific data বের করো:**
```bash
# শুধু Instance IDs দেখো
aws ec2 describe-instances \
  --query 'Reservations[*].Instances[*].InstanceId' \
  --output text

# শুধু running instances এর IPs দেখো
aws ec2 describe-instances \
  --filters "Name=instance-state-name,Values=running" \
  --query 'Reservations[*].Instances[*].PublicIpAddress' \
  --output text
```

**Help দেখো:**
```bash
# General help
aws help

# Service help
aws s3 help

# Command help
aws s3 cp help
```

**Current config দেখো:**
```bash
aws configure list
```

---

## 12. AWS CLI vs AWS Console

**বাংলা:** কখন Console use করবো আর কখন CLI use করবো।

**English:** Choosing between AWS Console and CLI depends on the use case.

| | AWS Console | AWS CLI |
|--|--|--|
| Learning | ভালো | কঠিন |
| Speed | ধীর | দ্রুত |
| Automation | সম্ভব না | সম্ভব |
| Scripting | সম্ভব না | সম্ভব |
| Visual | আছে | নাই |
| Beginners | ভালো | কঠিন |
| Production | কম use | বেশি use |

---

## 13. AWS CLI Common Errors

**বাংলা:** সাধারণত যে errors আসে আর সমাধান।

**English:** Common AWS CLI errors and their solutions.

**Error 1: credentials not found**
```bash
# সমাধান:
aws configure
# Access key আর Secret key দাও
```

**Error 2: region not set**
```bash
# সমাধান:
aws configure set region ap-southeast-1
```

**Error 3: Access Denied**
```bash
# সমাধান:
# IAM user এ সঠিক permission দাও
# Policy attach করো
```

**Error 4: command not found**
```bash
# সমাধান:
# AWS CLI আবার install করো
# PATH check করো
```

---

## 14. AWS CLI Automation Example

**বাংলা:** CLI দিয়ে automation করার একটা real example।

**English:** A real-world example of automation using AWS CLI.

**Daily S3 Backup Script:**
```bash
#!/bin/bash
# প্রতিদিন রাত ১২টায় backup নাও

DATE=$(date +%Y-%m-%d)
BUCKET="my-backup-bucket"
SOURCE="/var/www/html"

echo "Backup শুরু হচ্ছে: $DATE"

# S3 তে upload করো
aws s3 sync $SOURCE s3://$BUCKET/backup-$DATE/

echo "Backup সম্পন্ন!"
```

---

## 15. AWS CLI Cheat Sheet

**বাংলা:** সব important commands এক জায়গায়।

**English:** Quick reference for commonly used AWS CLI commands.

```bash
# ========== CONFIGURE ==========
aws configure                          # Setup credentials
aws configure list                     # Current config দেখো
aws configure list-profiles            # All profiles দেখো

# ========== S3 ==========
aws s3 ls                              # Buckets list
aws s3 mb s3://bucket-name             # Bucket বানাও
aws s3 cp file.txt s3://bucket/        # Upload
aws s3 cp s3://bucket/file.txt ./      # Download
aws s3 sync ./folder s3://bucket/      # Sync
aws s3 rm s3://bucket/file.txt         # Delete file
aws s3 rb s3://bucket --force          # Delete bucket

# ========== EC2 ==========
aws ec2 describe-instances             # Instances দেখো
aws ec2 start-instances --instance-ids i-xxx    # Start
aws ec2 stop-instances --instance-ids i-xxx     # Stop
aws ec2 terminate-instances --instance-ids i-xxx # Terminate

# ========== IAM ==========
aws iam list-users                     # Users দেখো
aws iam create-user --user-name NAME   # User বানাও
aws iam list-groups                    # Groups দেখো
aws iam list-roles                     # Roles দেখো

# ========== VPC ==========
aws ec2 describe-vpcs                  # VPCs দেখো
aws ec2 describe-subnets               # Subnets দেখো
aws ec2 describe-security-groups       # Security groups দেখো

# ========== GENERAL ==========
aws help                               # Help
aws [service] help                     # Service help
aws [service] [command] help           # Command help
```

---

## Key Takeaways

| Concept | এক কথায় |
|---------|---------|
| AWS CLI | Terminal থেকে AWS control |
| Install | Windows/Mac/Linux তে install হয় |
| Configure | Access Key দিয়ে setup করো |
| S3 commands | ls, cp, sync, rm, mb, rb |
| EC2 commands | describe, start, stop, terminate |
| IAM commands | list-users, create-user, list-roles |
| VPC commands | describe-vpcs, describe-subnets |
| Output format | json, table, text, yaml |
| Profiles | Multiple accounts manage করা |
| Automation | Script দিয়ে auto কাজ করা |
