
markdown# Day 08 - VPC Scenario Based Questions

## Date: 17 April 2026

---

## Q1 & Q2: How would you set up a Bastion Host to access private subnet instances?

**বাংলা:** Private subnet এর instances এ directly internet থেকে access করা যায় না। Bastion Host হলো একটা special EC2 instance যেটা public subnet এ থাকে। এইটার মাধ্যমে private subnet এ securely access করা যায়।

**English:** A Bastion Host (also called a Jump Server) is an EC2 instance in a public subnet that acts as a secure gateway to access instances in private subnets.

**Architecture:**
Internet
↓
Internet Gateway
↓
Bastion Host (Public Subnet) ← Admin SSH করে এখানে
↓
Private Instance (Private Subnet) ← Bastion থেকে এখানে যায়

**Setup Steps:**

**Step 1 — Bastion Host বানাও:**
- Public Subnet এ EC2 instance launch করো
- Security Group এ শুধু তোমার IP থেকে SSH (Port 22) allow করো
- Key Pair দিয়ে login করবে

**Step 2 — Private Instance এর Security Group:**
- Inbound SSH (Port 22) শুধু Bastion Host এর Security Group থেকে allow করো
- Direct internet থেকে কোনো access নাই

**Step 3 — Connect করো:**
```bash
# প্রথমে Bastion Host এ SSH করো
ssh -i key.pem ec2-user@bastion-public-ip

# তারপর Bastion থেকে Private Instance এ যাও
ssh -i key.pem ec2-user@private-instance-ip
```

**Security Best Practices:**
- Bastion Host এ MFA enable করো
- শুধু নির্দিষ্ট IP থেকে access দাও
- Session logs রাখো
- Use করা না হলে Bastion Host বন্ধ রাখো

**Real life:** Security guard এর মতো — সবাই সরাসরি office এ ঢুকতে পারে না, আগে security guard এর কাছে যেতে হয়।

---

## Q3: What is the difference between IAM Users, Groups, Roles and Policies?

**বাংলা:** IAM এর চারটা component আলাদা কাজ করে।

**English:** IAM has four main components, each serving a different purpose.

### IAM User
**বাংলা:** একজন মানুষ বা application এর permanent identity। Username আর password দিয়ে login করে।

**English:** A permanent identity representing a person or application with long-term credentials (username/password or access keys).

**উদাহরণ:** তোমার company তে Salim নামে একজন developer — সে একটা IAM User।

### IAM Group
**বাংলা:** অনেকগুলো User এর collection। Group কে permission দিলে সব User পায়।

**English:** A collection of IAM users. Permissions assigned to a group are automatically applied to all users in that group.

**উদাহরণ:** "Developer-Group" এ ৫ জন developer — একবার permission দিলে সবাই পায়।

### IAM Role
**বাংলা:** Temporary identity। কোনো নির্দিষ্ট মানুষের না — AWS services বা applications use করে।

**English:** A temporary identity that can be assumed by AWS services, applications, or users from other accounts. No permanent credentials.

**উদাহরণ:** EC2 instance কে S3 access দিতে Role use করো।

### IAM Policy
**বাংলা:** JSON document যেখানে permissions লেখা থাকে। কে কী করতে পারবে সেটা define করে।

**English:** A JSON document that defines permissions — what actions are allowed or denied on which AWS resources.

**উদাহরণ:**
```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

### সহজে মনে রাখো:

| Component | মানে | উদাহরণ |
|-----------|------|--------|
| User | একজন মানুষ | Salim (developer) |
| Group | দলবদ্ধ মানুষ | Developer Team |
| Role | Temporary পরিচয় | EC2 → S3 access |
| Policy | Rules এর document | S3 read করতে পারবে |

---

## Q4: What is the difference between NACL and Security Group? Explain with a use case.

**বাংলা:** NACL subnet level এ কাজ করে, Security Group instance level এ কাজ করে।

**English:** NACL operates at the subnet level while Security Group operates at the instance level.

### পার্থক্য:

| Feature | NACL | Security Group |
|---------|------|---------------|
| Level | Subnet | Instance |
| Stateful/Stateless | Stateless | Stateful |
| Rules | Allow + Deny | শুধু Allow |
| Evaluation | Rule number অনুযায়ী | সব rules একসাথে |
| Default | সব allow | Outbound allow, Inbound deny |

### Use Case:

**Scenario:** তোমার VPC তে একটা public subnet আছে যেখানে web server আছে।

**NACL দিয়ে:**
- Subnet level এ block করো — নির্দিষ্ট IP range থেকে সব traffic block
- যেমন: 192.168.1.0/24 থেকে সব traffic deny করো

**Security Group দিয়ে:**
- Instance level এ control করো — শুধু Port 80 আর 443 allow
- যেমন: Web server এ HTTP আর HTTPS allow করো

**একসাথে use করলে:**
Internet Traffic
↓
NACL (Subnet এর দরজা) → Malicious IP block করো
↓
Security Group (Instance এর দরজা) → শুধু HTTP/HTTPS allow
↓
Web Server EC2

---

## Q5: Application needs to access S3 securely within VPC. How would you achieve this?

**বাংলা:** VPC থেকে S3 তে internet ছাড়াই securely access করতে **VPC Endpoint** use করবো।

**English:** Use a VPC Endpoint (specifically a Gateway Endpoint for S3) to allow secure access to S3 without traffic leaving the AWS network.

**Solution — VPC Gateway Endpoint:**

**Step 1 — VPC Endpoint বানাও:**
- VPC → Endpoints → Create Endpoint
- Service: com.amazonaws.region.s3
- Type: Gateway
- তোমার VPC select করো
- Route Table select করো

**Step 2 — IAM Role বানাও:**
- EC2 instance কে S3 access দেওয়ার জন্য Role বানাও
- Policy: AmazonS3ReadOnlyAccess বা custom policy

**Step 3 — S3 Bucket Policy:**
```json
{
  "Effect": "Deny",
  "Principal": "*",
  "Action": "s3:*",
  "Resource": "arn:aws:s3:::my-bucket/*",
  "Condition": {
    "StringNotEquals": {
      "aws:sourceVpce": "vpce-xxxxxxxx"
    }
  }
}
```

**Benefits:**
- Traffic internet এ যায় না — AWS network এর ভেতরে থাকে
- বেশি secure
- NAT Gateway এর charge লাগে না
- Fast connection

---

## Q6: How would you set up an isolated environment for sensitive workloads?

**বাংলা:** Sensitive workload এর জন্য Private Subnet এ isolated environment বানাবো।

**English:** Create an isolated environment using private subnets with strict security controls for sensitive workloads.

**Solution:**

**Step 1 — Dedicated VPC বানাও:**
- আলাদা CIDR block দাও: 10.1.0.0/16
- Production এর সাথে mix করো না

**Step 2 — Private Subnet বানাও:**
- Internet Gateway নাই
- NAT Gateway নাই (যদি internet দরকার না হয়)
- Completely isolated

**Step 3 — Strict Security Group:**
- শুধু নির্দিষ্ট source থেকে traffic allow
- সব outbound traffic deny

**Step 4 — NACL configure করো:**
- Specific IP range ছাড়া সব deny
- Outbound ও restrict করো

**Step 5 — VPC Flow Logs enable করো:**
- সব traffic monitor করো
- CloudWatch এ logs পাঠাও

**Architecture:**
Isolated VPC (10.1.0.0/16)
↓
Private Subnet (No IGW, No NAT)
↓
Sensitive EC2 Instances
↓
Strict Security Group + NACL

---

## Q7: How would you implement strict network access control for VPC resources?

**বাংলা:** VPC resources এ strict access control implement করতে NACL, Security Group আর IAM একসাথে use করবো।

**English:** Implement strict network access control using a combination of NACLs, Security Groups, and IAM policies as defense in depth.

**Solution — Defense in Depth:**

**Layer 1 — NACL (Subnet level):**
- Specific IP ranges allow করো
- Known malicious IPs deny করো
- Unnecessary ports block করো
Rule 100: Allow TCP 443 from 0.0.0.0/0
Rule 200: Allow TCP 80 from 0.0.0.0/0
Rule 300: Deny all from 192.168.0.0/16 (malicious range)
Rule *: Deny all

**Layer 2 — Security Group (Instance level):**
- Minimum required ports only open করো
- Source specific করো

**Layer 3 — IAM (API level):**
- Least privilege principle follow করো
- MFA enforce করো

**Layer 4 — VPC Flow Logs:**
- সব traffic monitor করো
- Suspicious activity detect করো

---

## Q8: EC2 instances communicate with each other using private IP addresses. What steps would you take?

**বাংলা:** Same VPC তে EC2 instances automatically private IP দিয়ে communicate করতে পারে। শুধু Security Group configure করতে হবে।

**English:** EC2 instances within the same VPC can communicate using private IP addresses by default, as long as security group rules allow the traffic.

**Steps:**

**Step 1 — Same VPC তে launch করো:**
- দুইটা EC2 instance same VPC তে launch করো
- Same বা আলাদা subnet এ হতে পারে

**Step 2 — Security Group configure করো:**
- Instance A এর Security Group এ Instance B থেকে traffic allow করো
- Source হিসেবে Instance B এর Security Group ID দাও
Instance A Security Group:
Inbound: All traffic from Instance-B-Security-Group-ID
Instance B Security Group:
Inbound: All traffic from Instance-A-Security-Group-ID

**Step 3 — Private IP দিয়ে communicate করো:**
```bash
# Instance A থেকে Instance B তে ping করো
ping 10.0.1.50  # Instance B এর private IP
```

**Note:** Internet দরকার নাই — VPC এর local network এর মধ্যেই হয়।

---

## Q9: Private subnet instances need internet access for software updates. How would you allow this?

**বাংলা:** Private subnet থেকে internet access দিতে NAT Gateway use করবো।

**English:** Use a NAT Gateway in the public subnet to allow instances in the private subnet to access the internet for outbound connections only.

**Solution:**

**Step 1 — NAT Gateway বানাও:**
- Public Subnet এ NAT Gateway create করো
- Elastic IP attach করো

**Step 2 — Private Subnet Route Table update করো:**
- 0.0.0.0/0 → NAT Gateway add করো

**Step 3 — Security Group update করো:**
- Outbound HTTPS (443) allow করো
- Outbound HTTP (80) allow করো

**Architecture:**
Private EC2
↓
NAT Gateway (Public Subnet)
↓
Internet Gateway
↓
Internet (Software Update Server)

**Important:**
- Internet থেকে Private EC2 তে সরাসরি আসা যাবে না
- শুধু Private EC2 থেকে internet এ যেতে পারবে
- NAT Gateway এ charge আছে

---

## Q10: Restrict outbound internet for one subnet but allow for another. How?

**বাংলা:** আলাদা Route Table দিয়ে এইটা করা যায়। একটা subnet এ NAT Gateway route রাখবো, অন্যটায় রাখবো না।

**English:** Use separate route tables for each subnet — one with a NAT Gateway route for internet access and one without for restricted access.

**Solution:**

**Subnet A (internet access চাই):**
- Route Table A তে add করো:
  - 0.0.0.0/0 → NAT Gateway

**Subnet B (internet access চাই না):**
- Route Table B তে শুধু:
  - 10.0.0.0/16 → local
  - 0.0.0.0/0 route নাই

**NACL দিয়েও করা যায়:**
- Subnet B এর NACL এ outbound internet deny করো
Subnet A Route Table:        Subnet B Route Table:
10.0.0.0/16 → local         10.0.0.0/16 → local
0.0.0.0/0 → NAT Gateway     (No internet route)

---

## Q11: Design a VPC architecture for a 2-tier highly available and scalable application.

**বাংলা:** 2-tier application মানে Web Tier আর Database Tier। Highly available মানে Multi-AZ। Scalable মানে Auto Scaling।

**English:** A 2-tier architecture separates the web/application layer from the database layer. High availability requires Multi-AZ deployment and scalability requires Auto Scaling.

**Architecture Design:**
Internet
↓
Route 53 (DNS)
↓
Application Load Balancer (ALB)
↓              ↓
Public Subnet    Public Subnet
(AZ-1)          (AZ-2)
↓              ↓
Auto Scaling Group (Web Servers)
EC2 (AZ-1)      EC2 (AZ-2)
↓              ↓
Private Subnet   Private Subnet
(AZ-1)          (AZ-2)
↓              ↓
RDS Primary     RDS Standby
(AZ-1)          (AZ-2 - Multi-AZ)

**VPC Setup:**
- CIDR: 10.0.0.0/16
- 2 AZs: AZ-1, AZ-2

**Subnets:**
| Subnet | AZ | CIDR | Type |
|--------|-----|------|------|
| Public-1 | AZ-1 | 10.0.1.0/24 | Public |
| Public-2 | AZ-2 | 10.0.2.0/24 | Public |
| Private-1 | AZ-1 | 10.0.3.0/24 | Private |
| Private-2 | AZ-2 | 10.0.4.0/24 | Private |

**Components:**

**Tier 1 — Web Layer:**
- Application Load Balancer (Public Subnet)
- Auto Scaling Group (EC2 in Public Subnet)
- Launch Template দিয়ে EC2 config
- Min: 2, Max: 10 instances

**Tier 2 — Database Layer:**
- RDS MySQL/PostgreSQL (Private Subnet)
- Multi-AZ enabled
- Automatic failover

**Security:**
- ALB Security Group: Port 80, 443 from internet
- EC2 Security Group: Port 80 from ALB only
- RDS Security Group: Port 3306 from EC2 only

**High Availability:**
- Multi-AZ deployment
- Auto Scaling — traffic বাড়লে server বাড়বে
- RDS Multi-AZ — database failover automatic

**Scalability:**
- Auto Scaling Group — CPU 70% এর উপরে গেলে নতুন instance
- Load Balancer — traffic distribute করে

---

## Key Takeaways

| Scenario | Solution |
|----------|---------|
| Private instance access | Bastion Host |
| Internet ছাড়া S3 access | VPC Endpoint |
| Private subnet internet | NAT Gateway |
| Strict access control | NACL + Security Group + IAM |
| Instance communication | Same VPC + Security Group |
| Sensitive workload | Isolated Private Subnet |
| Subnet internet restrict | Separate Route Tables |
| Highly available app | Multi-AZ + Auto Scaling + ALB |
