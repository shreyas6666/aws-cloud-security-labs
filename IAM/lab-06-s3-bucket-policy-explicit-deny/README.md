# Lab 6 — S3 Bucket Policy + Explicit Deny

## Objective

Understand how identity-based policies and resource-based policies work together, and how an explicit Deny overrides an Allow.

## Architecture

IAM User
   ↓
Identity-Based Policy
   ↓
S3 Bucket
   ↑
Bucket Policy

## What I Did

1. Created an IAM user named `iam-lab-user`.
2. Gave the user permission to read an S3 object using an identity-based policy.
3. Created an S3 bucket policy that allowed the same user to read objects.
4. Successfully accessed `secret.txt`.
5. Added an explicit `Deny` for `s3:GetObject`.
6. Tried to access `secret.txt` again.
7. Access was denied.

## Result

Before Explicit Deny:

`ALLOW → Access Granted`

After Explicit Deny:

`ALLOW + ALLOW + EXPLICIT DENY → ACCESS DENIED`

## Key Concepts

- Identity-based policy
- Resource-based policy
- S3 bucket policy
- Explicit Deny
- Policy evaluation
- Least privilege

## Key Takeaway

An explicit `Deny` overrides an `Allow`.
