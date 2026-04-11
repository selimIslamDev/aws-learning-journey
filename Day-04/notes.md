# Day 04 - Amazon VPC Complete Guide

## Date: 11 April 2026

---

## 1. What is VPC?

**বাংলা:** VPC মানে Virtual Private Cloud। এইটা হলো AWS এর ভেতরে তোমার নিজের আলাদা network। যেমন তোমার বাসায় আলাদা WiFi network আছে — VPC হলো AWS এর ভেতরে তোমার আলাদা private network। এই network এ তুমি তোমার EC2, RDS সব কিছু রাখতে পারবে।

**English:** VPC (Virtual Private Cloud) is a logically isolated section of the AWS cloud where you can launch AWS resources in a virtual network that you define. You have complete control over your networking environment.

---

## 2. What is Subnet?

**বাংলা:** Subnet মানে হলো VPC এর ভেতরে আরও ছোট ছোট ভাগ। যেমন একটা বড় অফিস building এ আলাদা আলাদা floor আছে — Subnet হলো সেই আলাদা floor। একটা VPC কে অনেকগুলো subnet এ ভাগ করা যায়।

**English:** A subnet is a range of IP addresses within your VPC. It allows you to divide your VPC into smaller network segments. Each subnet resides in one Availability Zone.

---

## 3. What is the difference between Public Subnet and Private Subnet?

**বাংলা:** Public Subnet মানে যে subnet এর সাথে Internet Gateway connected — মানে internet থেকে access করা যায়। Private Subnet মানে যে subnet internet থেকে directly access করা যায় না — শুধু ভেতরে ভেতরে communicate করতে পারে।

**English:** Public Subnet has a route to the Internet Gateway, allowing resources to communicate with the internet. Private Subnet has no direct route to the internet — resources here are isolated from public access.

**Real life উদাহরণ:**
- Public Subnet → Web server (সবাই access করতে পারবে)
- Private Subnet → Database (শুধু web server access করতে পারবে)

---

## 4. What is Internet Gateway?

**বাংলা:** Internet Gateway হলো তোমার VPC আর internet এর মধ্যে দরজা। এই দরজা না থাকলে তোমার VPC এর কোনো resource internet এ যেতে পারবে না বা internet থেকে কেউ আসতে পারবে না।

**English:** An Internet Gateway is a horizontally scaled, redundant, and highly available VPC component that allows communication between your VPC and the internet. Without it, no traffic can flow in or out of your VPC.

**Real life:** তোমার বাসার main gate — এই gate না থাকলে কেউ ঢুকতে বা বের হতে পারবে না।

---

## 5. What is Route Table?

**বাংলা:** Route Table হলো traffic এর map। এইটা বলে দেয় কোন traffic কোথায় যাবে। যেমন internet এর traffic Internet Gateway দিয়ে যাবে — এই নির্দেশ Route Table এ লেখা থাকে।

**English:** A Route Table contains a set of rules (routes) that determine where network traffic is directed. Each subnet must be associated with a route table that controls the routing for that subnet.

**উদাহরণ:**
- 0.0.0.0/0 → Internet Gateway (internet traffic)
- 10.0.0.0/16 → Local (VPC এর ভেতরের traffic)

---

## 6. What is NAT Gateway?

**বাংলা:** NAT Gateway হলো Private Subnet এর জন্য internet connection। Private Subnet এর resources নিজে internet এ যেতে পারে না — NAT Gateway এর মাধ্যমে internet এ যায়। কিন্তু internet থেকে কেউ সরাসরি Private Subnet এ ঢুকতে পারে না।

**English:** NAT (Network Address Translation) Gateway allows instances in a private subnet to connect to the internet or other AWS services, but prevents the internet from initiating a connection with those instances.

**Real life:** তুমি বাইরে যেতে পারবে কিন্তু বাইরের কেউ তোমার বাসায় আসতে পারবে না।

---

## 7. What is Security Group?

**বাংলা:** Security Group হলো instance level এর firewall। এইটা ঠিক করে কোন port এ কে traffic পাঠাতে পারবে। Security Group stateful — মানে outbound allow করলে সেই response automatically allowed।

**English:** A Security Group acts as a virtual firewall at the instance level. It controls inbound and outbound traffic for EC2 instances. Security Groups are stateful — if you allow inbound traffic, the response is automatically allowed outbound.

---

## 8. What is Network ACL (NACL)?

**বাংলা:** NACL মানে Network Access Control List। এইটা হলো Subnet level এর firewall। Security Group instance কে protect করে, NACL পুরো subnet কে protect করে। NACL stateless — inbound আর outbound আলাদা আলাদা rule লাগে।

**English:** Network ACL is an optional layer of security for your VPC that acts as a firewall at the subnet level. Unlike Security Groups, NACLs are stateless — you must define both inbound and outbound rules separately.

---

## 9. What is the difference between Security Group and NACL?

**বাংলা:** Security Group instance কে protect করে আর stateful। NACL subnet কে protect করে আর stateless।

**English:** Security Group vs NACL comparison.

| Feature | Security Group | NACL |
|---------|---------------|------|
| Level | Instance | Subnet |
| Stateful/Stateless | Stateful | Stateless |
| Rules | Allow only | Allow + Deny |
| Default | All outbound allowed | All traffic allowed |

---

## 10. What is VPC Peering?

**বাংলা:** VPC Peering হলো দুইটা VPC কে একসাথে connect করা। যেমন তোমার দুইটা আলাদা office আছে — VPC Peering দিয়ে দুইটা office এর network একসাথে connect করা যায়। এক VPC থেকে আরেক VPC তে directly communicate করা যায়।

**English:** VPC Peering is a networking connection between two VPCs that enables you to route traffic between them using private IPv4 or IPv6 addresses. Instances in either VPC can communicate with each other as if they are within the same network.

> ⚠️ **Important:** Overlapping CIDR blocks থাকলে VPC Peering করা যাবে না।

---

## 11. What is CIDR block?

**বাংলা:** CIDR মানে Classless Inter-Domain Routing। এইটা দিয়ে IP address এর range ঠিক করা হয়। যেমন 10.0.0.0/16 মানে হলো 10.0.0.0 থেকে 10.0.255.255 পর্যন্ত সব IP address।

**English:** CIDR (Classless Inter-Domain Routing) block defines the IP address range for your VPC or subnet. For example, 10.0.0.0/16 gives you 65,536 IP addresses ranging from 10.0.0.0 to 10.0.255.255.

**উদাহরণ:**
- /16 → 65,536 IP addresses (VPC এর জন্য)
- /24 → 256 IP addresses (Subnet এর জন্য)

---

## 12. What is Availability Zone and why is it important in VPC?

**বাংলা:** Availability Zone হলো একটা Region এর ভেতরে আলাদা আলাদা data center। যেমন Singapore region এ ap-southeast-1a, ap-southeast-1b, ap-southeast-1c আছে। VPC এ আলাদা AZ তে subnet রাখলে একটা AZ down হলেও অন্যটা চালু থাকে।

**English:** An Availability Zone is an isolated data center within a region. Each subnet in a VPC is associated with one AZ. Distributing resources across multiple AZs provides high availability — if one AZ fails, others continue to function.

---

## 13. Why is VPC Region Specific?

**বাংলা:** VPC region specific কারণ VPC এর resources physically একটা নির্দিষ্ট region এর data center এ থাকে। একটা VPC এক region এর বাইরে যেতে পারে না। তবে VPC Peering দিয়ে আলাদা region এর VPC connect করা যায়।

**English:** A VPC is bound to a specific AWS region because it is tied to the physical infrastructure of that region. Resources within a VPC cannot span multiple regions. However, inter-region VPC peering allows VPCs in different regions to communicate.

---

## 14. What is the default VPC in AWS?

**বাংলা:** AWS account খোলার সাথে সাথে প্রতিটা region এ একটা default VPC automatically বানানো থাকে। এই default VPC তে already Internet Gateway, Route Table, Subnet সব configure করা থাকে। তুমি চাইলে এইটা দিয়েই শুরু করতে পারো।

**English:** AWS automatically creates a default VPC in each region when you create your account. The default VPC comes pre-configured with an Internet Gateway, route tables, and subnets in each Availability Zone, making it easy to launch instances immediately.

---

## 15. What is Elastic IP and how does it relate to VPC?

**বাংলা:** Elastic IP হলো একটা static public IP address যেটা তোমার AWS account এ assign করা থাকে। EC2 instance বন্ধ করলে সাধারণ IP বদলে যায় কিন্তু Elastic IP সবসময় same থাকে। VPC এর public subnet এ Elastic IP attach করা যায়।

**English:** An Elastic IP is a static, public IPv4 address that you can associate with any instance or network interface in your VPC. Unlike regular public IPs that change when you stop/start an instance, Elastic IP remains constant.

> ⚠️ **Important:** Elastic IP use না করলে charge হয় — তাই use শেষ হলে release করো।

---

## Key Takeaways (1-15)

| Concept | এক কথায় |
|---------|---------|
| VPC | AWS এ নিজের private network |
| Subnet | VPC এর ছোট ভাগ |
| Public Subnet | Internet access আছে |
| Private Subnet | Internet access নাই |
| Internet Gateway | VPC আর internet এর দরজা |
| Route Table | Traffic এর map |
| NAT Gateway | Private subnet এর internet connection |
| Security Group | Instance এর firewall |
| NACL | Subnet এর firewall |
| VPC Peering | দুই VPC connect করা |
| CIDR | IP address range |
| AZ | আলাদা data center |
| Default VPC | Auto বানানো VPC |
| Elastic IP | Fixed public IP |
