---

## IAM Deep Dive

### 1. What is IAM?

**বাংলা:** IAM মানে Identity and Access Management। এইটা AWS এর একটা service যেটা দিয়ে তুমি ঠিক করতে পারো — কে AWS এ login করতে পারবে এবং login করার পর কে কী করতে পারবে। সহজ কথায় IAM হলো তোমার AWS account এর security guard + manager।

**English:** IAM (Identity and Access Management) is an AWS service that controls who can access your AWS account and what they can do. It manages users, groups, roles, and policies.

---

### 2. What is the main intention to create User?

**বাংলা:** User বানানোর মূল উদ্দেশ্য হলো প্রতিটা মানুষকে আলাদা পরিচয় দেওয়া। যেমন তোমার company তে ১০ জন developer আছে — প্রত্যেকের জন্য আলাদা user বানালে কে কী করলো সেটা track করা যায়। সবাই একই account দিয়ে login করলে কে কোন কাজ করেছে বোঝা যাবে না।

**English:** The main intention of creating a user is to give each person a unique identity in AWS. This allows you to track who did what, control individual access, and revoke access when someone leaves the company.

**Real life:** নতুন developer join করলে তার জন্য user বানাও। সে চাকরি ছেড়ে দিলে সেই user delete করো — তার access চলে যাবে।

---

### 3. What is the main intention to create Group?

**বাংলা:** Group বানানোর মূল উদ্দেশ্য হলো একই ধরনের কাজের মানুষদের একসাথে রেখে একবারে permission দেওয়া। ৫ জন developer কে আলাদা আলাদা permission না দিয়ে একটা "Developer-Group" বানিয়ে সেখানে permission দিলে সবাই একসাথে পেয়ে যায়।

**English:** The main intention of creating a group is to manage permissions for multiple users at once. Instead of assigning permissions to each user individually, you assign them to a group and all users in that group automatically get those permissions.

**Real life:** নতুন developer আসলে শুধু Developer-Group এ add করো — আলাদা করে permission দিতে হবে না।

---

### 4. What is the main intention to create Policy?

**বাংলা:** Policy বানানোর মূল উদ্দেশ্য হলো কে কী করতে পারবে সেটা clearly define করা। Policy হলো rules এর একটা document — এই document এ লেখা থাকে কোন service এ কী কাজ করা যাবে আর কী যাবে না।

**English:** The main intention of creating a policy is to define exactly what actions are allowed or denied on which AWS resources. A policy is a JSON document that specifies permissions.

**Real life example:**
- Developer policy → S3 read করতে পারবে, delete করতে পারবে না
- Admin policy → সব কিছু করতে পারবে
- Tester policy → শুধু দেখতে পারবে, কিছু change করতে পারবে না

---

### 5. What is the main intention to create Role?

**বাংলা:** Role বানানোর মূল উদ্দেশ্য হলো মানুষ ছাড়া অন্য AWS service গুলোকে permission দেওয়া। যেমন তোমার EC2 server কে S3 থেকে file পড়তে হবে — তখন EC2 কে একটা Role দেবে। Role হলো temporary permission — কাজ শেষে চলে যায়।

**English:** The main intention of creating a role is to grant temporary permissions to AWS services, applications, or users from other accounts. Unlike users, roles don't have permanent credentials — they provide temporary access when needed.

**Real life example:**
- EC2 server কে S3 access দিতে Role ব্যবহার করো
- Lambda function কে DynamoDB access দিতে Role ব্যবহার করো

---

### 6. What is Authentication?

**বাংলা:** Authentication মানে হলো "তুমি কে?" এটা prove করা। AWS এ login করার সময় username আর password দাও — AWS check করে এই credentials ঠিক আছে কিনা। এইটাই authentication।

**English:** Authentication is the process of verifying who you are. It confirms your identity using credentials like username and password before granting access to AWS.

**Real life:** তুমি যখন Gmail এ login করো — email আর password দাও, Gmail verify করে। এইটাই authentication।

---

### 7. What is Authorization?

**বাংলা:** Authorization মানে হলো "তুমি কী করতে পারবে?" এটা ঠিক করা। Login হওয়ার পরেও সব কিছু করতে পারবে না — policy দেখে ঠিক হয় কে কোথায় যেতে পারবে।

**English:** Authorization is the process of determining what actions you are allowed to perform after authentication. It checks your permissions and decides what resources you can access.

**Real life:** Office এ ঢোকার পর (authentication) সব room এ যেতে পারবে না — boss এর room এ permission লাগবে (authorization)।

---

### 8. What is MFA and Why is it Important?

**বাংলা:** MFA মানে Multi-Factor Authentication। শুধু password দিয়ে login না করে আরেকটা extra verification দিতে হয়। যেমন password দেওয়ার পর phone এ একটা code আসে — সেই code ও দিতে হয়। তাহলে কেউ password চুরি করলেও account এ ঢুকতে পারবে না কারণ phone তো তার কাছে নাই।

**English:** MFA (Multi-Factor Authentication) adds an extra layer of security by requiring a second form of verification in addition to your password. Even if someone steals your password, they cannot access your account without the second factor.

**কেন Important:**
- শুধু password weak — easily hack হতে পারে
- MFA দিলে account অনেক বেশি secure হয়
- AWS strongly recommend করে root account এ MFA enable রাখতে

**Real life:** ATM এ card (password) + PIN (second factor) — দুইটাই লাগে।

---

### 9. What is AWS CLI?

**বাংলা:** AWS CLI মানে Command Line Interface। সাধারণত AWS Console এ গিয়ে click করে কাজ করি। কিন্তু CLI দিয়ে terminal এ command লিখে একই কাজ করা যায়। Developer দের জন্য এইটা অনেক দ্রুত এবং powerful।

**English:** AWS CLI (Command Line Interface) is a tool that allows you to interact with AWS services using commands in your terminal instead of clicking through the AWS Console. It's faster and can be automated with scripts.

**Example:**
- Console এ: IAM → Users → Create user → form fill করো
- CLI তে: মাত্র একটা command
```bash
aws iam create-user --user-name salim-dev
```

**কেন শিখবে:**
- অনেক দ্রুত কাজ করা যায়
- Automation করা যায়
- Real job এ CLI জানা অনেক important
