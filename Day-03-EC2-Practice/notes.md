
# Day 03 - Amazon EC2 Hands-on Practice

## Date: 10 April 2026

---

## What I practiced today

Today I completed the "Introduction to Amazon EC2" lab on AWS Skill Builder.
I launched a real web server on AWS and accessed it from the internet.

---

## Task 1: Launch EC2 Instance

**Steps I followed:**
- Went to EC2 → Launch instance
- Name: Web Server
- AMI: Amazon Linux 2023
- Instance type: t3.micro
- Key pair: Proceed without a key pair
- Network: Lab VPC → Public Subnet 1
- Security group: Web Server security group
- Termination protection: Enabled
- User data script:

```bash
#!/bin/bash
dnf -y install httpd
systemctl enable httpd
systemctl start httpd
echo '<html><h1>Hello From Your Web Server!</h1></html>' > /var/www/html/index.html
```

**What happened:**
- EC2 instance launched successfully
- Apache web server installed automatically via user data
- Instance state: Running
- Status check: 3/3 checks passed

---

## Task 2: Monitor EC2 Instance

**What I checked:**
- Status and alarms tab → System, Instance, EBS checks all passed ✅
- Monitoring tab → CloudWatch metrics দেখলাম
  - CPU utilization: 10.4%
  - Network in/out: active
- System log → server boot log দেখলাম
- Instance screenshot → server এর console দেখলাম

**What I learned:**
CloudWatch দিয়ে EC2 instance monitor করা যায়। CPU, Network, Disk সব কিছু track করা যায়।

---

## Task 3: Update Security Group and Access Web Server

**Problem:**
Security group এ কোনো inbound rule ছিল না — তাই website access হচ্ছিল না।

**Solution:**
- Security Groups → Web Server security group → Edit inbound rules
- Type: HTTP, Port: 80, Source: Anywhere (0.0.0.0/0)
- Save rules

**Result:**
Browser এ `http://35.87.42.244` লিখলাম — দেখলাম:
> **Hello From Your Web Server!** 🎉

**What I learned:**
Security Group হলো firewall। Port 80 open না করলে HTTP traffic ঢুকতে পারে না।

---

## Task 4: Resize Instance and EBS Volume

### Change Instance Type
- Instance stop করলাম
- Actions → Instance settings → Change instance type
- t3.micro → t3.small (2x memory)
- Applied successfully ✅

### Resize EBS Volume
- Elastic Block Store → Volumes
- Actions → Modify volume
- 8 GiB → 10 GiB
- Modified successfully ✅

- Instance আবার start করলাম

**What I learned:**
EC2 instance stop করে instance type change করা যায়। EBS volume চলতে চলতেও বাড়ানো যায়।

---

## Task 5: Test Termination Protection

**What I did:**
- Instance terminate করতে গেলাম
- Error আসলো — Termination protection enabled আছে ✅
- Actions → Instance settings → Change termination protection → Disable
- আবার terminate করলাম — সফলভাবে terminated হলো ✅

**What I learned:**
Termination protection দিলে accidentally instance delete হয় না। Production এ সবসময় এটা enable রাখা উচিত।

---

## Key Takeaways

| Topic | যা শিখলাম |
|-------|-----------|
| EC2 Launch | AMI, Instance type, Key pair, Security group, User data |
| Monitoring | CloudWatch metrics, System log, Instance screenshot |
| Security Group | Inbound rules দিয়ে traffic control করা যায় |
| Resize | Instance type আর EBS volume change করা যায় |
| Termination Protection | Accidental delete থেকে রক্ষা করে |

---

## Commands used in User Data

```bash
#!/bin/bash
dnf -y install httpd        # Apache web server install
systemctl enable httpd      # Boot এ auto start
systemctl start httpd       # এখনই start
echo '<html><h1>Hello From Your Web Server!</h1></html>' > /var/www/html/index.html  # Web page বানানো
```
