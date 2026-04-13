# Day 06 - Amazon Route 53

## Date: 13 April 2026

---

## Route 53 কী এবং এর Main কাজ কী?

**বাংলা:** Route 53 হলো AWS এর DNS (Domain Name System) service। সহজ কথায় — তুমি যখন browser এ "google.com" লিখো, Route 53 সেই নামকে IP address এ convert করে। যেমন "google.com" → "142.250.80.46"। এছাড়া Route 53 দিয়ে domain কিনতে পারো, health check করতে পারো, আর traffic routing করতে পারো।

**English:** Amazon Route 53 is a highly available and scalable cloud Domain Name System (DNS) web service. It translates human-readable domain names (like www.example.com) into IP addresses (like 192.0.2.1) that computers use to connect to each other.

**Route 53 এর ৩টা Main কাজ:**
1. **Domain Registration** — domain name কিনতে পারো
2. **DNS Routing** — domain কে IP address এ convert করে
3. **Health Checking** — resource healthy আছে কিনা check করে

**Real life:** তোমার phone book এর মতো — নাম দিলে number বের করে দেয়।

---

## Route 53 Questions & Answers

---

### 1. What is DNS?

**বাংলা:** DNS মানে Domain Name System। এইটা হলো internet এর phone book। তুমি "facebook.com" লিখলে DNS সেটাকে Facebook এর server এর IP address এ convert করে।

**English:** DNS (Domain Name System) is the internet's phonebook. It translates domain names that humans can remember (like facebook.com) into IP addresses that computers use to communicate (like 157.240.22.35).

**উদাহরণ:**
- তুমি লিখলে → facebook.com
- DNS convert করলো → 157.240.22.35
- তোমার browser সেই IP তে গেলো

---

### 2. What is Route 53?

**বাংলা:** Route 53 হলো AWS এর DNS service। এইটা দিয়ে domain register করা যায়, DNS routing করা যায়, আর health check করা যায়। নামটা এসেছে DNS এর default port 53 থেকে।

**English:** Amazon Route 53 is AWS's highly available and scalable DNS web service. The name "Route 53" refers to TCP/UDP port 53, the port used for DNS requests. It provides domain registration, DNS routing, and health checking capabilities.

---

### 3. What is a Hosted Zone?

**বাংলা:** Hosted Zone হলো একটা container যেখানে তোমার domain এর সব DNS records থাকে। যেমন "example.com" এর জন্য একটা Hosted Zone বানালে — সেখানে সব records (A, CNAME, MX etc.) রাখা হয়।

**English:** A Hosted Zone is a container for DNS records for a specific domain. It tells Route 53 how to respond to DNS queries for a domain and its subdomains.

**Types:**
- **Public Hosted Zone** → internet থেকে access করা যায়
- **Private Hosted Zone** → শুধু VPC এর ভেতরে কাজ করে

---

### 4. What are DNS Record Types?

**বাংলা:** DNS Record হলো domain এর বিভিন্ন তথ্য। বিভিন্ন ধরনের record আছে।

**English:** DNS Records are instructions that live in DNS servers and provide information about a domain.

| Record Type | কাজ | উদাহরণ |
|------------|-----|--------|
| **A** | Domain → IPv4 address | example.com → 1.2.3.4 |
| **AAAA** | Domain → IPv6 address | example.com → 2001:db8::1 |
| **CNAME** | Domain → অন্য domain | www → example.com |
| **MX** | Email server | mail.example.com |
| **TXT** | Text information | SPF, DKIM verification |
| **NS** | Name server | কোন server এ records আছে |
| **SOA** | Zone এর authority info | Zone এর admin info |
| **Alias** | AWS resource → domain | ELB, CloudFront, S3 |

---

### 5. What is the difference between A Record and CNAME Record?

**বাংলা:** A Record সরাসরি IP address দেয়। CNAME অন্য domain এর দিকে point করে।

**English:** An A Record maps a domain name directly to an IPv4 address. A CNAME (Canonical Name) record maps a domain name to another domain name.

| | A Record | CNAME Record |
|--|--|--|
| Points to | IP address | Another domain |
| Root domain | ✅ Use করা যায় | ❌ Use করা যায় না |
| উদাহরণ | example.com → 1.2.3.4 | www.example.com → example.com |

> 💡 **Tip:** Root domain (example.com) এ CNAME দেওয়া যায় না — Alias record দিতে হয়।

---

### 6. What is an Alias Record?

**বাংলা:** Alias record হলো Route 53 এর special record। এইটা domain কে AWS resource (ELB, CloudFront, S3) এর দিকে point করায়। CNAME এর মতো কিন্তু root domain এও use করা যায় এবং free।

**English:** An Alias record is a Route 53 specific extension to DNS. It routes traffic to AWS resources like ELB, CloudFront distributions, S3 buckets, and more. Unlike CNAME, it can be used at the root domain level and has no additional charge.

**উদাহরণ:**
- example.com → myapp.us-east-1.elb.amazonaws.com

---

### 7. What are Routing Policies in Route 53?

**বাংলা:** Routing Policy মানে DNS query আসলে Route 53 কিভাবে respond করবে সেটা। বিভিন্ন ধরনের routing policy আছে।

**English:** Routing policies determine how Route 53 responds to DNS queries. Different policies serve different use cases.

| Policy | কাজ |
|--------|-----|
| Simple | একটা resource এ traffic পাঠায় |
| Weighted | % ভাগে traffic ভাগ করে |
| Latency | সবচেয়ে কম latency তে পাঠায় |
| Failover | Primary down হলে Secondary তে যায় |
| Geolocation | User এর location অনুযায়ী পাঠায় |
| Geoproximity | Distance অনুযায়ী পাঠায় |
| Multivalue | অনেকগুলো IP return করে |

---

### 8. What is Simple Routing Policy?

**বাংলা:** Simple routing মানে একটাই record, একটাই resource। যেমন example.com → 1.2.3.4। কোনো special logic নাই।

**English:** Simple routing policy routes traffic to a single resource. You can specify multiple values in the same record, and Route 53 returns all values to the user in random order.

**Use case:** Single server, simple website।

---

### 9. What is Weighted Routing Policy?

**বাংলা:** Weighted routing দিয়ে traffic কে ভাগ করা যায়। যেমন 70% traffic Server A তে, 30% traffic Server B তে। নতুন version deploy করার সময় কাজে লাগে।

**English:** Weighted routing lets you associate multiple resources with a single domain and choose how much traffic is routed to each resource. You can assign weights from 0 to 255 to each record.

**Use case:** A/B testing, gradual deployment।

**উদাহরণ:**
- Server A → weight 70 (70% traffic)
- Server B → weight 30 (30% traffic)

---

### 10. What is Latency Routing Policy?

**বাংলা:** Latency routing মানে user কে সবচেয়ে কাছের server এ পাঠানো। Bangladesh এর user → Singapore server, USA এর user → Virginia server।

**English:** Latency routing policy routes traffic to the AWS region that provides the lowest latency for the user. Route 53 measures latency between users and AWS regions and routes accordingly.

**Use case:** Global application যেখানে speed important।

---

### 11. What is Failover Routing Policy?

**বাংলা:** Failover routing মানে Primary server down হলে automatically Secondary server এ চলে যাওয়া। Health check করে দেখে Primary চালু আছে কিনা।

**English:** Failover routing policy configures active-passive failover. Route 53 routes traffic to the primary resource when it's healthy, and automatically switches to the secondary resource when the primary is unhealthy.

**Use case:** High availability, disaster recovery।
