# EC2 Auto Scaling Complete Guide

## Date: 15 April 2026

---

## Auto Scaling কেন দরকার এবং Main কাজ কী?

**বাংলা:** Auto Scaling মানে হলো traffic অনুযায়ী automatically server বাড়ানো বা কমানো। যেমন ঈদের দিন তোমার website এ হঠাৎ লক্ষ মানুষ আসলে — Auto Scaling নিজেই নতুন server চালু করবে। রাতে traffic কমলে server বন্ধ করে দেবে। তোমাকে manually কিছু করতে হবে না।

**English:** Auto Scaling automatically adjusts the number of EC2 instances based on demand. It adds instances when traffic increases and removes them when traffic decreases, ensuring optimal performance and cost efficiency.

**Auto Scaling এর Main ৩টা কাজ:**

| কাজ | মানে |
|-----|------|
| Scale Out | Traffic বাড়লে server বাড়াও |
| Scale In | Traffic কমলে server কমাও |
| Self Healing | Server crash করলে নতুন বানাও |

**Real life:** Daraz এ sale এর সময় হঠাৎ লক্ষ মানুষ আসে — Auto Scaling সেই সময় নিজেই server বাড়িয়ে দেয়, sale শেষে কমিয়ে দেয়।

---

## 1. What is EC2 Auto Scaling?

**বাংলা:** EC2 Auto Scaling হলো AWS এর service যেটা automatically EC2 instances বাড়ায় বা কমায়। CPU বেশি হলে নতুন instance আসে, কম হলে চলে যায়।

**English:** Amazon EC2 Auto Scaling helps you maintain application availability and allows you to automatically add or remove EC2 instances according to conditions you define.

---

## 2. What are the components of Auto Scaling?

**বাংলা:** Auto Scaling এর ৩টা main component আছে।

**English:** Auto Scaling consists of three main components.

| Component | কাজ |
|-----------|-----|
| Launch Template | EC2 instance এর blueprint |
| Auto Scaling Group | Instance গুলো manage করে |
| Scaling Policy | কখন বাড়াবে বা কমাবে |

---

## 3. What is Launch Template?

**বাংলা:** Launch Template হলো EC2 instance বানানোর recipe। এইখানে AMI, Instance Type, Security Group, User Data সব define করা থাকে। Auto Scaling Group এই template দিয়ে নতুন instance বানায়।

**English:** A Launch Template is a reusable configuration that specifies the instance configuration information needed to launch EC2 instances in an Auto Scaling group, including AMI, instance type, key pair, security groups, and user data.

```yaml
# Launch Template এ যা থাকে:
AMI ID: ami-0abcdef1234567890
Instance Type: t2.micro
Key Pair: my-key-pair
Security Group: web-server-sg
User Data: #!/bin/bash...
