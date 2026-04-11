# Advanced VPC Concepts

## Date: 12 April 2026

---

## 16. What is VPC Endpoint?

**বাংলা:** VPC Endpoint হলো এমন একটা connection যেটা দিয়ে তোমার VPC থেকে AWS services (যেমন S3, DynamoDB) এ internet ছাড়াই connect করা যায়। মানে তোমার private subnet থেকে S3 তে file রাখতে internet লাগবে না — VPC Endpoint দিয়ে directly যাবে।

**English:** A VPC Endpoint enables private connections between your VPC and AWS services without requiring internet gateway, NAT device, VPN connection, or AWS Direct Connect. Traffic between your VPC and the AWS service does not leave the Amazon network.

**Types:**
- **Interface Endpoint** → Most AWS services এর জন্য
- **Gateway Endpoint** → S3 আর DynamoDB এর জন্য (free)

---

## 17. What is Transit Gateway?

**বাংলা:** Transit Gateway হলো একটা central hub যেটা দিয়ে অনেকগুলো VPC আর on-premise network একসাথে connect করা যায়। VPC Peering এ শুধু দুইটা VPC connect হয়, কিন্তু Transit Gateway দিয়ে ১০০+ VPC একসাথে connect করা যায়।

**English:** AWS Transit Gateway is a network transit hub that connects VPCs and on-premises networks through a central hub. It simplifies network architecture by eliminating complex peering relationships between multiple VPCs.

**Real life:** ঢাকার কেন্দ্রীয় bus terminal — সব জায়গার bus এখানে আসে আর এখান থেকে সব জায়গায় যায়।

---

## 18. What is Site-to-Site VPN?

**বাংলা:** Site-to-Site VPN হলো তোমার office এর network আর AWS VPC এর মধ্যে একটা secure encrypted tunnel। মানে তোমার office থেকে AWS এর resources এ private ভাবে access করা যাবে — public internet দিয়ে গেলেও encrypted থাকবে।

**English:** AWS Site-to-Site VPN creates an encrypted tunnel between your on-premises network (office) and your AWS VPC over the public internet. It allows secure communication between your corporate network and AWS resources.

**Real life:** দুইটা অফিসের মধ্যে secure private phone line।

---

## 19. What is Direct Connect?

**বাংলা:** Direct Connect হলো তোমার office আর AWS এর মধ্যে একটা dedicated physical connection। VPN এর মতো internet দিয়ে না গিয়ে সরাসরি AWS এর network এ connect হয়। অনেক দ্রুত আর reliable।

**English:** AWS Direct Connect is a dedicated network connection from your premises to AWS. Unlike VPN which goes over the internet, Direct Connect uses a private, dedicated physical line — providing more consistent network performance and lower latency.

| | Site-to-Site VPN | Direct Connect |
|--|--|--|
| Connection | Internet এর উপর দিয়ে | Dedicated physical line |
| Speed | কম | বেশি |
| Cost | সস্তা | দামি |
| Setup | দ্রুত | সময় লাগে |

---

## 20. What is Flow Logs?

**বাংলা:** VPC Flow Logs হলো তোমার VPC এর সব network traffic এর record। কে কোথায় কতটুকু data পাঠালো — সব কিছু log হয়ে থাকে। Security analysis আর troubleshooting এ অনেক কাজে লাগে।

**English:** VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC. Flow logs can help you with security analysis, monitoring, and troubleshooting connectivity issues.

**কোথায় store হয়:** CloudWatch Logs অথবা S3

---

## 21. What is Bastion Host?

**বাংলা:** Bastion Host হলো একটা special EC2 instance যেটা public subnet এ থাকে। Private subnet এর servers এ directly internet থেকে access করা যায় না — তাই আগে Bastion Host এ login করতে হয়, তারপর সেখান থেকে private subnet এর server এ যাওয়া যায়।

**English:** A Bastion Host is a special-purpose EC2 instance in a public subnet that acts as a gateway to access instances in private subnets. It provides a secure way to SSH into private instances without exposing them to the internet.

**Real life:** অফিসের security guard — বাইরের কেউ সরাসরি ভেতরে যেতে পারবে না, security guard এর কাছে আগে যেতে হবে।

---

## 22. What is the difference between NAT Gateway and NAT Instance?

**বাংলা:** দুইটাই private subnet থেকে internet access দেয়, কিন্তু NAT Gateway AWS manage করে আর অনেক বেশি reliable। NAT Instance হলো একটা EC2 instance যেটা manually configure করতে হয়।

| Feature | NAT Gateway | NAT Instance |
|---------|------------|--------------|
| Management | AWS managed | Self managed |
| Availability | Highly available | Single point of failure |
| Performance | High | Limited |
| Cost | বেশি | কম |
| Recommended | ✅ Yes | ❌ No (outdated) |

---

## 23. What is Egress-Only Internet Gateway?

**বাংলা:** Egress-Only Internet Gateway হলো শুধু IPv6 এর জন্য NAT Gateway এর মতো। IPv6 resources internet এ যেতে পারবে কিন্তু internet থেকে কেউ সরাসরি আসতে পারবে না।

**English:** An Egress-Only Internet Gateway allows outbound communication over IPv6 from instances in your VPC to the internet, and prevents the internet from initiating an IPv6 connection with your instances.

> 💡 **Note:** শুধু IPv6 এর জন্য। IPv4 এর জন্য NAT Gateway use করো।

---

## 24. What is VPC DHCP Options?

**বাংলা:** DHCP Options হলো VPC এর network configuration settings। এইটা দিয়ে DNS server, domain name ইত্যাদি configure করা যায়। AWS automatically একটা default DHCP option set দেয়।

**English:** DHCP Options Sets allow you to configure network settings for instances in your VPC, such as DNS servers, domain names, and NTP servers. AWS provides a default DHCP options set for each VPC.

---

## 25. What is the difference between VPC and Subnet CIDR?

**বাংলা:** VPC CIDR হলো পুরো VPC এর IP address range। Subnet CIDR হলো সেই VPC এর ভেতরে একটা ছোট অংশের IP range। Subnet CIDR সবসময় VPC CIDR এর ভেতরে থাকতে হবে।

**English:** VPC CIDR defines the overall IP address range for the entire VPC. Subnet CIDR is a subset of the VPC CIDR, defining the IP range for a specific subnet within the VPC.

**উদাহরণ:**
- VPC CIDR: 10.0.0.0/16 (65,536 IPs)
- Public Subnet: 10.0.1.0/24 (256 IPs)
- Private Subnet: 10.0.2.0/24 (256 IPs)

---

## 26. What happens if two VPCs have overlapping CIDR blocks in VPC Peering?

**বাংলা:** দুইটা VPC এর CIDR block একই বা overlap করলে VPC Peering করা যাবে না। কারণ AWS বুঝতে পারবে না কোন traffic কোন VPC তে যাবে।

**English:** VPC Peering cannot be established between two VPCs with overlapping or matching CIDR blocks. AWS cannot route traffic correctly if two VPCs have the same IP address ranges, as it would create routing conflicts.

> ⚠️ **Important:** VPC বানানোর সময়ই আলাদা CIDR block দিতে হবে।

---

## 27. How many subnets can you create in a VPC?

**বাংলা:** একটা VPC তে maximum **200টা subnet** বানানো যায়। তবে সাধারণত প্রতিটা AZ তে একটা public আর একটা private subnet রাখা হয়।

**English:** You can create a maximum of 200 subnets per VPC by default. However, best practice is to create at least one public and one private subnet per Availability Zone for high availability.

**Best practice:**
- AZ-1 → 1 Public Subnet + 1 Private Subnet
- AZ-2 → 1 Public Subnet + 1 Private Subnet
- AZ-3 → 1 Public Subnet + 1 Private Subnet

---

## 28. What is Multi-AZ architecture and why is it important?

**বাংলা:** Multi-AZ architecture মানে হলো তোমার resources একটা AZ তে না রেখে একাধিক AZ তে ছড়িয়ে রাখা। যদি একটা AZ down হয়ে যায় — অন্যটা চালু থাকবে। এইটা high availability নিশ্চিত করে।

**English:** Multi-AZ architecture involves deploying resources across multiple Availability Zones within a region. If one AZ experiences an outage, resources in other AZs continue to operate, ensuring high availability and fault tolerance.

**Real life:** তোমার data দুইটা আলাদা জায়গায় backup রাখলে একটা নষ্ট হলেও অন্যটা থেকে restore করা যাবে।

---

## 29. What is the difference between Internet Gateway and NAT Gateway?

| Feature | Internet Gateway | NAT Gateway |
|---------|----------------|-------------|
| Direction | দুই দিক (in + out) | এক দিক (out only) |
| Subnet | Public Subnet | Private Subnet |
| Internet access | সরাসরি | একমুখী |
| কে use করে | Public resources | Private resources |
| Cost | Free | Charge আছে |

---

## 30. How does traffic flow from a private subnet to the internet?

**বাংলা:** Private subnet থেকে internet এ যাওয়ার flow:
1. Private subnet এর EC2 → NAT Gateway (Public Subnet এ)
2. NAT Gateway → Internet Gateway
3. Internet Gateway → Internet

Internet থেকে সরাসরি Private Subnet এ ঢোকা যায় না।

**English:** Traffic flow from private subnet to internet:
