# Lab 4 — EC2 IAM Role → S3

## Objective

Learn how an EC2 workload can securely access an S3 bucket using an IAM Role instead of storing long-term AWS access keys on the EC2 instance.

---

## Architecture

```text
EC2
 │
 │ assumes
 ▼
IAM Role: EC2-S3-Read-Role
 │
 │ s3:GetObject
 ▼
S3 Bucket
 │
 ▼
secret.txt
```

---

## What I Built

* Created an S3 bucket.
* Uploaded `secret.txt`.
* Created an IAM Role named `EC2-S3-Read-Role`.
* Configured EC2 as the trusted service.
* Created a least-privilege permission policy.
* Allowed only `s3:GetObject` on the lab bucket.
* Attached the IAM Role to an EC2 instance.
* Connected to the EC2 instance using EC2 Instance Connect.
* Verified the identity being used by EC2.
* Successfully downloaded the S3 object.
* Attempted to delete the object and verified that AWS denied the request.

---

# 1. S3 Resource

### Configuration

S3 bucket:

```text
ec2-iam-role-lab-yourname-2026
```

Object:

```text
secret.txt
```

### Evidence

![S3 bucket containing secret.txt](screenshots/01-s3-bucket.png)

**What this proves:**
The S3 bucket and test object were created successfully.

---

# 2. IAM Role

### Role

```text
EC2-S3-Read-Role
```

### Trusted Entity

```text
EC2
```

The trust relationship allows the EC2 service to assume the role.

### Evidence

![IAM Role](screenshots/02-iam-role.png)

**What this proves:**
The role exists and is intended to be assumed by EC2.

---

# 3. Least-Privilege Permission

The role was given permission to perform:

```text
s3:GetObject
```

on objects inside the lab bucket.

The role was intentionally NOT given:

```text
s3:DeleteObject
```

### Permission concept

```text
s3:GetObject       → Allowed
s3:DeleteObject    → Not allowed
```

### Evidence

![IAM role permissions](screenshots/03-role-permissions.png)

**What this proves:**
The role was configured with a restricted S3 permission rather than broad S3 access.

---

# 4. IAM Role Attached to EC2

The IAM instance profile attached to the EC2 instance was:

```text
EC2-S3-Read-Role
```

### Evidence

![EC2 IAM role](screenshots/04-ec2-role-attached.png)

**What this proves:**
The EC2 instance was configured to use the IAM Role.

---

# 5. Verify the EC2 Identity

From inside the EC2 terminal:

```bash
aws sts get-caller-identity
```

### Result

The response returned an ARN containing:

```text
assumed-role/EC2-S3-Read-Role/
```

### Evidence

![STS identity verification](screenshots/05-sts-identity.png)

**What this proves:**

The EC2 instance was operating through the IAM Role rather than manually configured IAM user credentials.

---

# 6. Test — Read S3 Object

Command:

```bash
aws s3 cp s3://ec2-iam-role-lab-yourname-2026/secret.txt .
```

### Result

```text
download: s3://ec2-iam-role-lab-yourname-2026/secret.txt to ./secret.txt
```

### Evidence

![Successful S3 download](screenshots/06-s3-download-success.png)

**Result: PASS ✅**

The EC2 instance successfully retrieved the object from S3.

This demonstrates that:

```text
EC2
 ↓
IAM Role
 ↓
s3:GetObject
 ↓
S3
```

was working as intended.

---

# 7. Test — Attempt Unauthorized Delete

Command:

```bash
aws s3 rm s3://ec2-iam-role-lab-yourname-2026/secret.txt
```

### Result

AWS returned:

```text
AccessDenied
```

The response specifically stated that the assumed role was not authorized to perform:

```text
s3:DeleteObject
```

### Evidence

![Delete denied](screenshots/07-delete-access-denied.png)

**Result: PASS ✅**

The deletion was denied because the role did not have `s3:DeleteObject` permission.

---

# 8. Test Results

| Test                 | Expected               | Actual                          | Result |
| -------------------- | ---------------------- | ------------------------------- | ------ |
| EC2 assumes IAM Role | Role identity returned | `assumed-role/EC2-S3-Read-Role` | ✅ PASS |
| Read S3 object       | Allowed                | Download successful             | ✅ PASS |
| Delete S3 object     | Denied                 | `AccessDenied`                  | ✅ PASS |

---

# 9. Security Lessons

### IAM Role

Allows an AWS workload such as EC2 to obtain permissions without storing long-term AWS access keys on the server.

### Trust Policy

Answers:

> Who can assume the role?

In this lab:

```text
EC2 → trusted
```

### Permissions Policy

Answers:

> What can the role do?

In this lab:

```text
s3:GetObject → allowed
```

### Least Privilege

The EC2 workload received only the permission required for the lab.

```text
Read → ✅
Delete → ❌
```

---

# 10. Key Takeaway

This lab demonstrates a common secure AWS pattern:

```text
Workload
   ↓
IAM Role
   ↓
Temporary credentials
   ↓
Least-privilege permissions
   ↓
AWS resource
```

No long-term AWS access keys were manually configured on the EC2 instance.

---

## Evidence Summary

The following evidence was collected during the lab:

1. S3 bucket and object
2. IAM Role configuration
3. IAM permission configuration
4. IAM Role attached to EC2
5. STS identity verification
6. Successful S3 object download
7. Unauthorized delete attempt resulting in `AccessDenied`
