# Day 10 - AWS CLI Advanced Questions

## Date: 20 April 2026

---

## 1. What is the difference between AWS CLI v1 and v2?

**বাংলা:** CLI v2 হলো latest version — অনেক নতুন features আছে আর বেশি fast।

**English:** AWS CLI v2 is the latest major version with significant improvements over v1.

| Feature | CLI v1 | CLI v2 |
|---------|--------|--------|
| Installation | pip দিয়ে | Standalone installer |
| Python requirement | Python লাগে | Built-in Python |
| SSO support | নাই | আছে |
| Auto-prompt | নাই | আছে |
| Speed | কম | বেশি |
| Recommended | ❌ | ✅ |

**Check version:**
```bash
aws --version
# aws-cli/2.x.x Python/3.x.x
```

---

## 2. What is AWS CLI Named Profiles and how to use them?

**বাংলা:** Named Profiles দিয়ে একাধিক AWS account manage করা যায়। যেমন development আর production এর জন্য আলাদা profile।

**English:** Named profiles allow you to store multiple sets of credentials and configurations, making it easy to switch between different AWS accounts or roles.

```bash
# Development profile বানাও
aws configure --profile dev
# Production profile বানাও
aws configure --profile prod

# Profile use করো
aws s3 ls --profile dev
aws ec2 describe-instances --profile prod

# Default profile set করো
export AWS_PROFILE=dev

# ~/.aws/credentials file এ থাকে:
[default]
aws_access_key_id = KEY1
aws_secret_access_key = SECRET1

[dev]
aws_access_key_id = KEY2
aws_secret_access_key = SECRET2

[prod]
aws_access_key_id = KEY3
aws_secret_access_key = SECRET3
```

---

## 3. How do you use AWS CLI with MFA?

**বাংলা:** MFA দিয়ে AWS CLI use করতে temporary credentials নিতে হয়।

**English:** To use AWS CLI with MFA, you need to obtain temporary security credentials using STS (Security Token Service).

```bash
# Step 1 — MFA device ARN খোঁজো
aws iam list-mfa-devices --user-name salim-dev

# Step 2 — Temporary credentials নাও
aws sts get-session-token \
  --serial-number arn:aws:iam::123456789:mfa/salim-dev \
  --token-code 123456

# Step 3 — Credentials export করো
export AWS_ACCESS_KEY_ID=TEMPORARY_KEY
export AWS_SECRET_ACCESS_KEY=TEMPORARY_SECRET
export AWS_SESSION_TOKEN=TEMPORARY_TOKEN

# Step 4 — এখন CLI use করো
aws s3 ls
```

---

## 4. What is AWS CLI Pagination and how to handle it?

**বাংলা:** AWS CLI বড় results কে pages এ ভাগ করে দেখায়। সব results একসাথে পেতে pagination handle করতে হয়।

**English:** When AWS CLI returns large amounts of data, it paginates the results. You need to handle pagination to retrieve all results.

```bash
# Auto pagination (সব results একসাথে)
aws ec2 describe-instances --no-paginate

# Manual pagination
aws s3api list-objects \
  --bucket my-bucket \
  --max-items 10

# Next page এর জন্য
aws s3api list-objects \
  --bucket my-bucket \
  --max-items 10 \
  --starting-token NEXT_TOKEN

# Page size set করো
aws ec2 describe-instances --page-size 10
```

---

## 5. What is the difference between `aws s3` and `aws s3api`?

**বাংলা:** `aws s3` হলো high-level commands — সহজ কিন্তু কম control। `aws s3api` হলো low-level commands — বেশি control কিন্তু জটিল।

**English:** `aws s3` provides high-level commands for common operations, while `aws s3api` provides low-level direct API access with more granular control.

| | aws s3 | aws s3api |
|--|--------|-----------|
| Level | High-level | Low-level |
| Simplicity | সহজ | জটিল |
| Control | কম | বেশি |
| Use case | Daily tasks | Advanced operations |

```bash
# aws s3 (সহজ)
aws s3 cp file.txt s3://my-bucket/

# aws s3api (বেশি control)
aws s3api put-object \
  --bucket my-bucket \
  --key file.txt \
  --body file.txt \
  --server-side-encryption AES256
```

---

## 6. How do you filter AWS CLI output using `--query`?

**বাংলা:** `--query` flag দিয়ে output থেকে specific data বের করা যায়। JMESPath syntax use করে।

**English:** The `--query` parameter uses JMESPath syntax to filter and format the output from AWS CLI commands.

```bash
# Instance IDs দেখো
aws ec2 describe-instances \
  --query 'Reservations[*].Instances[*].InstanceId' \
  --output text

# Instance ID আর State দেখো
aws ec2 describe-instances \
  --query 'Reservations[*].Instances[*].[InstanceId,State.Name]' \
  --output table

# Specific bucket এর name দেখো
aws s3api list-buckets \
  --query 'Buckets[*].Name' \
  --output text

# Running instances এর Public IP দেখো
aws ec2 describe-instances \
  --filters "Name=instance-state-name,Values=running" \
  --query 'Reservations[*].Instances[*].PublicIpAddress' \
  --output text

# IAM users এর name আর creation date
aws iam list-users \
  --query 'Users[*].[UserName,CreateDate]' \
  --output table
```

---

## 7. What is AWS CLI `--dry-run` flag?

**বাংলা:** `--dry-run` flag দিয়ে command actually run না করে test করা যায়। Permission আছে কিনা check করতে use হয়।

**English:** The `--dry-run` flag checks whether you have the required permissions to perform an operation without actually making the API request.

```bash
# Instance launch এর permission আছে কিনা check করো
aws ec2 run-instances \
  --image-id ami-12345678 \
  --instance-type t2.micro \
  --dry-run

# Permission আছে হলে:
# An error occurred (DryRunOperation) — এইটা actually success মানে!

# Permission নাই হলে:
# An error occurred (UnauthorizedOperation)
```

---

## 8. How do you use AWS CLI with IAM Roles instead of access keys?

**বাংলা:** Access keys এর বদলে IAM Role use করা বেশি secure। EC2 instance তে role attach করলে automatically credentials পাওয়া যায়।

**English:** Using IAM roles instead of access keys is more secure as credentials are automatically rotated and you don't need to store keys in files.

**EC2 instance তে:**
```bash
# EC2 তে role attach করো → automatically credentials পাবে
# No need for aws configure

# Verify করো
aws sts get-caller-identity
```

**Role assume করো:**
```bash
# Role assume করো
aws sts assume-role \
  --role-arn arn:aws:iam::123456789:role/my-role \
  --role-session-name my-session

# Credentials export করো
export AWS_ACCESS_KEY_ID=TEMP_KEY
export AWS_SECRET_ACCESS_KEY=TEMP_SECRET
export AWS_SESSION_TOKEN=TEMP_TOKEN
```

---

## 9. What is AWS CloudShell and how is it different from AWS CLI?

**বাংলা:** CloudShell হলো browser এর মধ্যে terminal। AWS Console এ directly CLI commands run করা যায়। Install করতে হয় না।

**English:** AWS CloudShell is a browser-based shell that provides command-line access to AWS services directly from the AWS Management Console without needing to install or configure CLI locally.

| Feature | AWS CLI | AWS CloudShell |
|---------|---------|----------------|
| Installation | নিজে install করতে হয় | Install লাগে না |
| Location | তোমার computer এ | AWS Console এ |
| Credentials | Configure করতে হয় | Auto configured |
| Storage | Unlimited | 1GB persistent |
| Access | Anywhere | Browser |
| Cost | Free | Free (500 min/month) |

**CloudShell কোথায় পাবে:**
AWS Console → উপরে ">_" icon click করো

---

## 10. How do you automate AWS CLI commands using Shell Scripts?

**বাংলা:** Shell scripts দিয়ে AWS CLI commands automate করা যায়। যেমন রোজ automatic backup নেওয়া।

**English:** Shell scripts can automate repetitive AWS CLI tasks, making infrastructure management more efficient.

**Example 1 — Daily Backup Script:**
```bash
#!/bin/bash
DATE=$(date +%Y-%m-%d)
SOURCE_BUCKET="production-data"
BACKUP_BUCKET="backup-data"

echo "Backup শুরু: $DATE"
aws s3 sync s3://$SOURCE_BUCKET s3://$BACKUP_BUCKET/backup-$DATE/
echo "Backup সম্পন্ন!"
```

**Example 2 — EC2 Auto Start/Stop:**
```bash
#!/bin/bash
INSTANCE_ID="i-1234567890abcdef0"

# রাত ১০টায় stop করো
aws ec2 stop-instances --instance-ids $INSTANCE_ID
echo "Instance stopped: $INSTANCE_ID"

# সকাল ৯টায় start করো
aws ec2 start-instances --instance-ids $INSTANCE_ID
echo "Instance started: $INSTANCE_ID"
```

**Example 3 — S3 Bucket Create এবং Configure:**
```bash
#!/bin/bash
BUCKET_NAME="my-project-bucket-$(date +%s)"
REGION="ap-southeast-1"

# Bucket বানাও
aws s3 mb s3://$BUCKET_NAME --region $REGION

# Versioning enable করো
aws s3api put-bucket-versioning \
  --bucket $BUCKET_NAME \
  --versioning-configuration Status=Enabled

echo "Bucket ready: $BUCKET_NAME"
```

---

## 11. What is the `--no-cli-pager` flag?

**বাংলা:** বড় output automatically pager (less/more) এ দেখায়। `--no-cli-pager` দিলে সরাসরি terminal এ দেখায়।

**English:** By default, AWS CLI v2 sends output through a pager program. The `--no-cli-pager` flag disables this behavior and outputs directly to the terminal.

```bash
# Pager ছাড়া output দেখো
aws ec2 describe-instances --no-cli-pager

# Permanently disable করো
aws configure set cli_pager ""
```

---

## 12. How do you handle AWS CLI errors and exceptions?

**বাংলা:** Script এ error handle করতে exit codes আর error messages check করতে হয়।

**English:** Proper error handling in AWS CLI scripts ensures reliability and easier debugging.

```bash
#!/bin/bash

# Command run করো
aws s3 cp file.txt s3://my-bucket/

# Error check করো
if [ $? -eq 0 ]; then
  echo "✅ Upload সফল!"
else
  echo "❌ Upload ব্যর্থ!"
  exit 1
fi

# Error output capture করো
ERROR=$(aws s3 cp file.txt s3://my-bucket/ 2>&1)
if [ $? -ne 0 ]; then
  echo "Error: $ERROR"
fi
```

**Common Exit Codes:**
| Code | মানে |
|------|------|
| 0 | Success |
| 1 | General error |
| 2 | Parse error |
| 130 | Ctrl+C দিয়ে cancel |
| 255 | Service error |

---

## 13. What is AWS CLI `wait` command?

**বাংলা:** `wait` command দিয়ে কোনো resource ready হওয়া পর্যন্ত অপেক্ষা করা যায়। যেমন EC2 instance running হওয়া পর্যন্ত wait করো।

**English:** The `wait` command pauses execution until a specific condition is met, which is useful when you need to wait for resources to reach a desired state before proceeding.

```bash
# Instance running হওয়া পর্যন্ত wait করো
aws ec2 wait instance-running \
  --instance-ids i-1234567890abcdef0

echo "Instance এখন running!"

# Instance stopped হওয়া পর্যন্ত wait করো
aws ec2 wait instance-stopped \
  --instance-ids i-1234567890abcdef0

# S3 bucket exist হওয়া পর্যন্ত wait করো
aws s3api wait bucket-exists \
  --bucket my-bucket

# Stack create complete হওয়া পর্যন্ত wait করো
aws cloudformation wait stack-create-complete \
  --stack-name my-stack
```

---

## 14. How do you use Environment Variables with AWS CLI?

**বাংলা:** Environment variables দিয়ে AWS credentials আর config set করা যায়। CI/CD pipeline এ এইভাবে করা হয়।

**English:** Environment variables can be used to set AWS credentials and configuration without using the credentials file, which is useful for CI/CD pipelines.

```bash
# Credentials set করো
export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG
export AWS_DEFAULT_REGION=ap-southeast-1

# Session token (MFA এর জন্য)
export AWS_SESSION_TOKEN=AQoDYXdzEJr...

# Verify করো
aws sts get-caller-identity

# Unset করো
unset AWS_ACCESS_KEY_ID
unset AWS_SECRET_ACCESS_KEY
```

**Priority order:**
