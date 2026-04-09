# Day 03 - Amazon EC2 Complete Guide

## Date: 08 April 2026

---

## 1. What is EC2?

**বাংলা:** EC2 মানে Elastic Compute Cloud। সহজ কথায় এইটা হলো AWS এর virtual computer। তোমার নিজের computer এ যেমন RAM, CPU, Storage আছে — EC2 তেও একই জিনিস আছে, কিন্তু এইটা internet এ AWS এর data center এ আছে। তুমি ভাড়ায় use করবে।

**English:** EC2 (Elastic Compute Cloud) is a virtual server in the cloud. Instead of buying a physical computer, you rent a virtual machine from AWS and use it over the internet.

**Real life:** তোমার একটা website চালাতে হবে — নিজে server কিনলে লক্ষ টাকা লাগবে। AWS EC2 তে মাসে কয়েকশো টাকায় একটা server পাবে।

---

## 2. What is AMI?

**বাংলা:** AMI মানে Amazon Machine Image। এইটা হলো একটা ready-made template — যেখানে operating system আর software আগে থেকেই install করা আছে। তুমি শুধু এই template দিয়ে server চালু করবে।

**English:** AMI (Amazon Machine Image) is a pre-configured template that contains the operating system and software needed to launch an EC2 instance. It's like a blueprint for your server.

**Real life:** নতুন computer কিনলে Windows install করতে হয় — AMI হলো সেই Windows এর CD, যেটা দিয়ে server চালু হয়।

| AMI Type | কাজ |
|----------|-----|
| Ubuntu AMI | Linux server |
| Windows AMI | Windows server |
| Amazon Linux AMI | AWS এর নিজস্ব Linux |

---

## 3. What is Instance Type?

**বাংলা:** Instance Type মানে হলো তোমার server কতটা powerful হবে সেটা। যেমন একটা ছোট website চালাতে কম power লাগবে, কিন্তু Netflix এর মতো বড় সাইট চালাতে অনেক বেশি power লাগবে। Instance Type দিয়ে তুমি ঠিক করো কতটুকু CPU আর RAM নেবে।

**English:** Instance Type defines the hardware configuration of your EC2 instance — how much CPU, RAM, and network capacity it has. Different types are optimized for different use cases.

| Instance Type | কাজ |
|--------------|-----|
| t2.micro | ছোট project, free tier |
| t3.medium | মাঝারি website |
| c5.large | বেশি processing দরকার |
| r5.large | বেশি RAM দরকার |

---

## 4. What is Key Pair?

**বাংলা:** Key Pair হলো তোমার server এ login করার চাবি। দুইটা জিনিস থাকে — Public Key (AWS রাখে) আর Private Key (তুমি রাখো)। তোমার Private Key ছাড়া কেউ server এ ঢুকতে পারবে না।

**English:** A Key Pair is a set of security credentials used to connect to your EC2 instance. It consists of a public key (stored by AWS) and a private key (downloaded by you). Without the private key, no one can access your server.

**Real life:** তালা (Public Key) AWS এর server এ লাগানো আছে — চাবি (Private Key) শুধু তোমার কাছে। চাবি ছাড়া কেউ ঢুকতে পারবে না।

> ⚠️ **Important:** Private Key একবারই download করা যায়। হারিয়ে ফেললে আর পাওয়া যাবে না।

---

## 5. What is Security Group?

**বাংলা:** Security Group হলো তোমার server এর firewall। এইটা ঠিক করে কোন port এ কে access করতে পারবে। যেমন তুমি চাইলে শুধু তোমার IP থেকে server এ ঢোকার permission দিতে পারো।

**English:** A Security Group acts as a virtual firewall for your EC2 instance. It controls inbound (incoming) and outbound (outgoing) traffic by defining rules for which ports and IP addresses are allowed.

| Port | কাজ |
|------|-----|
| 22 | SSH দিয়ে server এ login |
| 80 | HTTP website |
| 443 | HTTPS website |
| 3306 | MySQL database |

---

## 6. What is EBS?

**বাংলা:** EBS মানে Elastic Block Store। এইটা হলো তোমার EC2 server এর hard disk। তোমার computer এ যেমন hard disk এ files রাখো — EC2 এর files রাখা হয় EBS এ।

**English:** EBS (Elastic Block Store) is the hard disk for your EC2 instance. It stores your data persistently, meaning even if you stop your EC2 instance, your data remains safe in EBS.

**Real life:** তোমার laptop বন্ধ করলেও files থাকে — EBS এও EC2 বন্ধ করলেও data থাকে।

---

## 7. What is Storage?

**বাংলা:** AWS এ storage মানে data রাখার জায়গা। EC2 এর জন্য দুই ধরনের storage আছে।

**English:** Storage in AWS refers to where your data is kept. For EC2, there are two main types of storage.

| Storage Type | বৈশিষ্ট্য |
|-------------|-----------|
| EBS | Permanent — server বন্ধ হলেও data থাকে |
| Instance Store | Temporary — server বন্ধ হলে data চলে যায় |

---

## 8. What is Elastic IP?

**বাংলা:** সাধারণত EC2 server বন্ধ করে আবার চালু করলে IP address বদলে যায়। Elastic IP হলো একটা fixed IP address যেটা সবসময় same থাকে — server বন্ধ করলেও IP বদলায় না।

**English:** An Elastic IP is a static, fixed public IP address that you can attach to your EC2 instance. Unlike regular IPs that change every time you restart your instance, Elastic IP stays the same permanently.

**Real life:** তোমার phone number যেমন সবসময় same থাকে — Elastic IP ও তেমনি সবসময় same থাকে।

> ⚠️ **Important:** Elastic IP use না করলে charge হয় — তাই use শেষ হলে release করে দাও।

---

## 9. Why is EC2 Service Region Specific?

**বাংলা:** AWS এর data center সারা পৃথিবীতে ছড়িয়ে আছে — America, Europe, Asia সব জায়গায়। Region মানে হলো কোন দেশে বা এলাকায় তোমার server থাকবে। EC2 region specific কারণ — তোমার server physically কোনো একটা জায়গায় থাকতে হবে। Bangladesh এর user দের জন্য Singapore বা Tokyo region এ server রাখলে দ্রুত response পাবে।

**English:** EC2 is region specific because AWS has physical data centers in different geographical locations around the world. Your EC2 instance runs on physical hardware in a specific location. Choosing the right region reduces latency, meets data compliance requirements, and improves performance for your users.

| কারণ | ব্যাখ্যা |
|------|---------|
| Speed | Users এর কাছের region = দ্রুত website |
| Cost | কিছু region অন্যটার চেয়ে সস্তা |
| Compliance | কিছু দেশে data সেই দেশেই রাখতে হয় |

**Bangladesh এর জন্য best region:**
- `ap-southeast-1` → Singapore
- `ap-northeast-1` → Tokyo

---

---

## Additional EC2 Concepts

### 10. What is the difference between Stop and Terminate?

**বাংলা:**
- **Stop** → server বন্ধ করা কিন্তু delete হয় না। আবার চালু করা যাবে। EBS data থাকবে।
- **Terminate** → server চিরতরে delete করা। আর ফিরে পাওয়া যাবে না।

**English:**
- **Stop** → Shuts down the instance but keeps it. You can restart it later. EBS data is preserved.
- **Terminate** → Permanently deletes the instance. Cannot be recovered.

> ⚠️ **Important:** Terminate করার আগে সবসময় নিশ্চিত হও — undo করা যাবে না।

---

### 11. What is the difference between Public IP and Private IP?

**বাংলা:**
- **Public IP** → Internet থেকে access করা যায়। বাইরের মানুষ এই IP দিয়ে তোমার server এ আসবে।
- **Private IP** → শুধু AWS এর ভেতরে use হয়। Internet থেকে access করা যায় না।

**English:**
- **Public IP** → Accessible from the internet. Used by external users to reach your server.
- **Private IP** → Only accessible within AWS network. Not reachable from the internet.

---

### 12. What is User Data in EC2?

**বাংলা:** User Data হলো একটা script যেটা EC2 server প্রথমবার চালু হওয়ার সময় automatically run হয়। যেমন server চালু হলেই automatically Nginx install হবে — আলাদা করে করতে হবে না।

**English:** User Data is a script that runs automatically when an EC2 instance starts for the first time. It's used to automate software installation and configuration without manual intervention.

**Example:**
```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
```

---

### 13. What is Auto Scaling?

**বাংলা:** Auto Scaling মানে হলো traffic বাড়লে automatically নতুন server যোগ হবে, traffic কমলে server কমে যাবে। যেমন ঈদের দিন তোমার website এ হঠাৎ অনেক মানুষ আসলে auto scaling নিজেই নতুন server চালু করবে।

**English:** Auto Scaling automatically adjusts the number of EC2 instances based on demand. It adds more instances when traffic increases and removes them when traffic decreases, ensuring performance and cost efficiency.

**Real life:** Daraz এ sale এর সময় হঠাৎ লক্ষ মানুষ আসে — Auto Scaling সেই সময় নিজেই server বাড়িয়ে দেয়।

---

### 14. What is Load Balancer?

**বাংলা:** Load Balancer হলো একটা traffic manager। যেমন তোমার ৩টা server আছে — Load Balancer সব users এর request সমানভাবে ৩টা server এ ভাগ করে দেয়। একটা server এ সব চাপ পড়ে না।

**English:** A Load Balancer distributes incoming traffic across multiple EC2 instances to ensure no single server gets overwhelmed. It improves performance, availability, and fault tolerance.

**Real life:** Shopping mall এ ৫টা cashier আছে — manager সমানভাবে সবার কাছে customer পাঠায়। এইটাই Load Balancer।

---

### 15. EC2 Pricing Models

**বাংলা:** EC2 এর ৩টা pricing model আছে।

**English:** EC2 has 3 main pricing models.

| Model | বাংলা | কখন use করবে |
|-------|-------|--------------|
| On-Demand | যখন use করবে তখন pay করবে | testing, short-term |
| Reserved | আগে থেকে বুক করলে সস্তা (১-৩ বছর) | production, long-term |
| Spot | unused server ভাড়া — অনেক সস্তা কিন্তু যেকোনো সময় বন্ধ হতে পারে | batch processing |

> 💡 **Tip:** শেখার সময় **On-Demand + Free Tier (t2.micro)** use করো — charge হবে না।

## Key Takeaways

- EC2 হলো AWS এর virtual server — ভাড়ায় use করা যায়
- AMI হলো server এর blueprint — OS আর software ready থাকে
- Instance Type দিয়ে server এর power ঠিক করা হয়
- Key Pair হলো server এ login করার চাবি — হারানো যাবে না
- Security Group হলো firewall — কে কোন port এ access করবে ঠিক করে
- EBS হলো server এর hard disk — permanent storage
- Elastic IP হলো fixed IP — server restart করলেও বদলায় না
- Region specific কারণ server physically কোথাও না কোথাও থাকতে হবে
