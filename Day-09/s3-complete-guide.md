# Day 09 - Amazon S3 Complete Guide

## Date: 19 April 2026

---

## S3 কেন দরকার এবং Main কাজ কী?

**বাংলা:** S3 মানে Simple Storage Service। এইটা হলো AWS এর object storage service। সহজ কথায় — internet এ files রাখার জায়গা। তোমার computer এ যেমন hard disk এ files রাখো — S3 হলো cloud এ সেই hard disk। যেকোনো ধরনের file রাখা যায় — image, video, document, code, backup সব কিছু।

**English:** Amazon S3 (Simple Storage Service) is an object storage service that offers industry-leading scalability, data availability, security, and performance. You can store and retrieve any amount of data from anywhere on the web.

**S3 এর Main ৫টা কাজ:**

| কাজ | উদাহরণ |
|-----|--------|
| Static website hosting | HTML, CSS, JS files |
| Backup and restore | Database backup রাখা |
| Data archiving | পুরনো data সংরক্ষণ |
| Media storage | Images, videos রাখা |
| Data lake | Big data analytics |

**Real life:** Google Drive বা Dropbox এর মতো — কিন্তু developers এর জন্য, programmatically access করা যায়।

---

## 1. What is Amazon S3?

**বাংলা:** S3 হলো AWS এর object storage service। এইখানে যেকোনো size এর যেকোনো ধরনের file রাখা যায়। প্রতিটা file কে "object" বলা হয় আর object গুলো "bucket" এ রাখা হয়।

**English:** Amazon S3 is an object storage service where you can store any type and amount of data. Data is stored as objects within containers called buckets. Each object consists of data, metadata, and a unique identifier.

---

## 2. What is an S3 Bucket?

**বাংলা:** Bucket হলো S3 এ files রাখার container। যেমন তোমার computer এ folder আছে — S3 এ bucket হলো সেই folder। প্রতিটা bucket এর একটা unique name থাকে — পুরো AWS এ same name আর কেউ use করতে পারবে না।

**English:** An S3 bucket is a container for storing objects (files) in Amazon S3. Bucket names must be globally unique across all AWS accounts and regions. You can create up to 100 buckets per AWS account by default.

**Bucket naming rules:**
- 3-63 characters
- Lowercase letters, numbers, hyphens only
- Must start with letter or number
- Globally unique

---

## 3. What is an S3 Object?

**বাংলা:** Object হলো S3 এ রাখা file। প্রতিটা object এ থাকে — actual data, metadata (file সম্পর্কে তথ্য), আর unique key (file এর নাম/path)।

**English:** An S3 object is a file stored in a bucket. Each object consists of:
- **Key** — object এর unique name/path
- **Value** — actual data
- **Metadata** — file সম্পর্কে তথ্য
- **Version ID** — versioning enabled থাকলে

**Maximum object size:** 5TB

---

## 4. What are S3 Storage Classes?

**বাংলা:** S3 এ বিভিন্ন ধরনের storage class আছে। বেশি access করলে বেশি দাম, কম access করলে কম দাম।

**English:** S3 offers different storage classes optimized for different use cases and access patterns.

| Storage Class | কখন use করবে | Cost |
|--------------|--------------|------|
| S3 Standard | Frequently accessed data | বেশি |
| S3 Intelligent-Tiering | Unknown access pattern | মাঝারি |
| S3 Standard-IA | Infrequently accessed | কম |
| S3 One Zone-IA | Non-critical, infrequent | আরও কম |
| S3 Glacier Instant | Archive, instant retrieval | অনেক কম |
| S3 Glacier Flexible | Archive, minutes retrieval | আরও কম |
| S3 Glacier Deep Archive | Long-term archive | সবচেয়ে কম |

---

## 5. What is S3 Versioning?

**বাংলা:** Versioning enable করলে একই file এর অনেকগুলো version রাখা যায়। যেমন একটা file update করলে আগের version ও থাকে। ভুলে delete করলেও restore করা যাবে।

**English:** S3 Versioning is a means of keeping multiple variants of an object in the same bucket. You can use versioning to preserve, retrieve, and restore every version of every object stored in your bucket.

**Use case:**
- Accidental delete থেকে protect করা
- File এর history রাখা
- Compliance requirement

---

## 6. What is S3 Lifecycle Policy?

**বাংলা:** Lifecycle Policy দিয়ে automatically files এক storage class থেকে অন্য storage class এ move করা যায়। যেমন ৩০ দিন পর Standard থেকে Glacier এ move করো।

**English:** S3 Lifecycle policies allow you to automatically transition objects between storage classes or expire objects after a specified time period.

**উদাহরণ:**
Day 0 → S3 Standard (active use)
Day 30 → S3 Standard-IA (less access)
Day 90 → S3 Glacier (archive)
Day 365 → Delete

---

## 7. What is S3 Bucket Policy?

**বাংলা:** Bucket Policy হলো JSON document যেটা দিয়ে bucket এর access control করা হয়। কে bucket access করতে পারবে, কী করতে পারবে সেটা define করা যায়।

**English:** A bucket policy is a resource-based IAM policy that you can use to grant access permissions to your S3 bucket and the objects in it.

**উদাহরণ — Public read access:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

---

## 8. What is S3 ACL (Access Control List)?

**বাংলা:** ACL হলো object বা bucket level এর access control। কোনো specific AWS account বা public কে access দিতে use করা হয়। তবে এখন Bucket Policy বেশি recommend করা হয়।

**English:** S3 ACLs are used to grant basic read/write permissions to other AWS accounts or public users. However, AWS now recommends using bucket policies instead of ACLs for most use cases.

---

## 9. What is S3 Encryption?

**বাংলা:** S3 তে data encrypted রাখা যায়। দুই ধরনের encryption আছে — data store হওয়ার সময় (at rest) আর transfer হওয়ার সময় (in transit)।

**English:** S3 supports encryption to protect your data both at rest and in transit.

**Types:**
| Type | মানে |
|------|------|
| SSE-S3 | AWS managed key দিয়ে encrypt |
| SSE-KMS | AWS KMS key দিয়ে encrypt |
| SSE-C | তোমার নিজের key দিয়ে encrypt |
| Client-side | Upload এর আগেই encrypt |

---

## 10. What is S3 Static Website Hosting?

**বাংলা:** S3 দিয়ে static website host করা যায়। HTML, CSS, JS files S3 এ রেখে website চালানো যায়। EC2 server লাগে না, অনেক সস্তা।

**English:** S3 can host static websites (HTML, CSS, JavaScript) without needing a web server. You simply enable static website hosting on your bucket, upload your files, and make them public.

**Steps:**
1. Bucket বানাও
2. Static website hosting enable করো
3. Index.html upload করো
4. Public access allow করো
5. Website URL পাবে

---

## 11. What is S3 Cross-Region Replication (CRR)?

**বাংলা:** CRR মানে একটা region এর bucket থেকে অন্য region এর bucket এ automatically copy করা। Disaster recovery আর compliance এর জন্য use করা হয়।

**English:** Cross-Region Replication automatically replicates objects from a source bucket in one AWS region to a destination bucket in a different region. Versioning must be enabled on both buckets.

**Use case:**
- Disaster recovery
- Compliance requirement
- Low latency access

---

## 12. What is S3 Transfer Acceleration?

**বাংলা:** Transfer Acceleration দিয়ে দূরের জায়গা থেকে S3 তে দ্রুত file upload করা যায়। AWS এর CloudFront edge locations use করে।

**English:** S3 Transfer Acceleration enables fast, easy, and secure transfers of files over long distances between your client and an S3 bucket. It uses Amazon CloudFront's globally distributed edge locations.

**Use case:** Bangladesh থেকে US region এর S3 বালতিতে দ্রুত upload করতে।

---

## 13. What is S3 Pre-signed URL?

**বাংলা:** Pre-signed URL হলো একটা temporary URL যেটা দিয়ে private S3 object access করা যায়। যেমন কাউকে একটা private file share করতে চাইলে — Pre-signed URL দাও, সে নির্দিষ্ট সময়ের মধ্যে access করতে পারবে।

**English:** A pre-signed URL grants temporary access to a private S3 object. The URL expires after a specified time period, making it a secure way to share private content without making the bucket public.

**উদাহরণ:**
```python
import boto3
s3 = boto3.client('s3')
url = s3.generate_presigned_url(
    'get_object',
    Params={'Bucket': 'my-bucket', 'Key': 'my-file.pdf'},
    ExpiresIn=3600  # 1 hour
)
```

---

## 14. What is S3 Event Notification?

**বাংলা:** S3 Event Notification মানে কোনো event হলে automatically notification পাঠানো। যেমন নতুন file upload হলে Lambda function trigger হবে।

**English:** S3 event notifications allow you to trigger actions when specific events occur in your bucket, such as when an object is created, deleted, or restored.

**Supported destinations:**
- Lambda function
- SQS queue
- SNS topic

**Use case:**
File upload হলো → S3 Event → Lambda trigger → Image resize করলো

---

## 15. What is the difference between S3 and EBS?

**বাংলা:** S3 হলো object storage — যেকোনো জায়গা থেকে access করা যায়। EBS হলো block storage — শুধু EC2 এর সাথে attached থাকে।

**English:** S3 and EBS serve different purposes in AWS storage.

| Feature | S3 | EBS |
|---------|-----|-----|
| Type | Object storage | Block storage |
| Access | Internet থেকে | শুধু EC2 থেকে |
| Use case | Files, media, backup | OS, database |
| Scalability | Unlimited | Limited (16TB max) |
| Pricing | Per GB stored | Per GB provisioned |

---

## 16. What is S3 Intelligent-Tiering?

**বাংলা:** Intelligent-Tiering automatically files কে frequent আর infrequent access tier এ move করে। তুমি manually কিছু করতে হবে না — AWS নিজেই optimize করে।

**English:** S3 Intelligent-Tiering automatically moves objects between access tiers based on changing access patterns, optimizing costs without performance impact or operational overhead.

---

## 17. What is S3 Object Lock?

**বাংলা:** Object Lock দিয়ে files delete বা overwrite করা prevent করা যায়। Compliance requirement এর জন্য use হয়।

**English:** S3 Object Lock prevents objects from being deleted or overwritten for a fixed amount of time or indefinitely. It uses a WORM (Write Once Read Many) model.

**Modes:**
- **Governance mode** → Admin override করতে পারে
- **Compliance mode** → কেউ override করতে পারে না

---

## 18. What is Multipart Upload?

**বাংলা:** বড় files (100MB+) upload করতে Multipart Upload use করা হয়। File কে অনেকগুলো part এ ভাগ করে parallel এ upload হয় — দ্রুত আর reliable।

**English:** Multipart upload allows you to upload a single object as a set of parts. Each part is a contiguous portion of the object's data. You can upload these object parts independently and in any order.

**Recommended for:** 100MB এর বেশি files

---

## 19. What is S3 Select?

**বাংলা:** S3 Select দিয়ে পুরো file download না করে SQL query দিয়ে specific data বের করা যায়। CSV, JSON, Parquet files এ কাজ করে।

**English:** S3 Select enables applications to retrieve only a subset of data from an object by using simple SQL expressions. This can improve performance and reduce costs by retrieving only the data needed.

---

## 20. What are S3 Best Practices?

**বাংলা:** S3 use করার সময় কিছু best practice follow করা উচিত।

**English:** Following S3 best practices ensures security, performance, and cost optimization.

**Security:**
- Block public access by default
- Encryption enable করো
- Bucket Policy দিয়ে access control করো
- MFA Delete enable করো

**Cost Optimization:**
- Lifecycle Policy use করো
- Intelligent-Tiering use করো
- Unused objects delete করো

**Performance:**
- Multipart upload use করো বড় files এর জন্য
- Transfer Acceleration use করো দূরের upload এর জন্য
- CloudFront দিয়ে static content deliver করো

**Reliability:**
- Versioning enable করো
- Cross-Region Replication setup করো
- Regular backups নাও

---

## Key Takeaways

| Concept | এক কথায় |
|---------|---------|
| S3 | Cloud এ files রাখার জায়গা |
| Bucket | Files এর container |
| Object | একটা file |
| Storage Classes | Access অনুযায়ী pricing |
| Versioning | File এর history রাখা |
| Lifecycle Policy | Auto storage class change |
| Bucket Policy | Access control |
| Encryption | Data secure রাখা |
| Static Hosting | Website host করা |
| CRR | অন্য region এ copy |
| Pre-signed URL | Temporary access |
| Event Notification | File upload এ trigger |
| Object Lock | Delete prevent করা |
| Multipart Upload | বড় file upload |
| S3 vs EBS | Object vs Block storage |
