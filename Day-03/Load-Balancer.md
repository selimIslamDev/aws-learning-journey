
# AWS Load Balancer

## Load Balancer কী?

Load Balancer হলো এমন একটি সার্ভিস যা incoming traffic কে একাধিক server/instance এ ভাগ করে দেয়, যাতে কোনো একটি server এ অতিরিক্ত চাপ না পড়ে।

---

## AWS-এ ৩ ধরনের Load Balancer

### ১. ALB — Application Load Balancer
- HTTP/HTTPS traffic এর জন্য
- Layer 7 (Application layer) এ কাজ করে
- URL path, header, host দেখে routing করতে পারে
- Web app, microservices এর জন্য সবচেয়ে বেশি ব্যবহৃত

### ২. NLB — Network Load Balancer
- TCP/UDP traffic এর জন্য
- Layer 4 (Network layer) এ কাজ করে
- অনেক দ্রুত, low latency
- Gaming, real-time app এর জন্য ভালো

### ৩. GLB — Gateway Load Balancer
- Third-party virtual appliances (firewall, IDS) এর জন্য
- Security inspection এর কাজে লাগে

---

## কীভাবে কাজ করে?

```
User Request
     ↓
Load Balancer
     ↓
┌────┬────┬────┐
EC2  EC2  EC2
 1    2    3
```

---

## মূল Components

| Component      | কাজ                                    |
|----------------|----------------------------------------|
| **Listener**   | কোন port/protocol এ traffic শুনবে      |
| **Target Group** | কোন server গুলোতে traffic পাঠাবে    |
| **Health Check** | server ঠিকঠাক আছে কিনা চেক করে     |
| **Rules**      | কোন traffic কোথায় যাবে               |

---

## সহজ উদাহরণ

ধরো তোমার একটি website আছে, ১০,০০০ user একসাথে ঢুকলো। একটি server এ সামলানো সম্ভব না। Load Balancer এই traffic কে ৫টি server এ ভাগ করে দেবে — প্রতিটিতে ২,০০০ user।
