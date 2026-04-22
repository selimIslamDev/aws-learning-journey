
# Day 10 - AWS CLI Advanced Questions

## Date: 20 April 2026

---

## 1. What is the difference between AWS CLI v1 and v2?

**বাংলা:** CLI v2 হলো latest version — অনেক নতুন features আছে আর বেশি fast।

**English:** AWS CLI v2 is the latest major version with significant improvements over v1.

| Feature | CLI v1 | CLI v2 |
|---------|--------|--------|
| Installation | pip দিয়ে | Standalone installer |
| Python requirement | Python লাগে | Built-in Python |
| SSO support | নাই | আছে |
| Auto-prompt | নাই | আছে |
| Speed | কম | বেশি |
| Recommended | ❌ | ✅ |

**Check version:**
```bash
aws --version
# aws-cli/2.x.x Python/3.x.x
