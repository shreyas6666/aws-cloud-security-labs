# Lab 07 — IAM Policy Simulator & Least Privilege

## Objective

Use the AWS IAM Policy Simulator to test permissions and verify that a least-privilege S3 policy allows only the required actions.

## What I tested

- `s3:ListBucket` → Allowed
- `s3:GetObject` → Allowed
- `s3:DeleteObject` → Denied

## Result

The IAM Policy Simulator confirmed that the user can list the bucket and read objects, but cannot delete objects.

This demonstrates the principle of **least privilege**.
