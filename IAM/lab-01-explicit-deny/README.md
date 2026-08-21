# Lab 1 — Explicit Deny

## What I wanted to learn

I wanted to understand what happens when one IAM policy allows an action but another policy denies it.

## What I created

I created two IAM policies:

* An Allow policy for S3 actions
* A Deny policy for `s3:DeleteObject`

I attached both policies to the IAM user I used for testing.

## What I tested

List S3 objects: Allowed

Upload object: Allowed

Delete object: Denied

## What I learned

The explicit Deny overrides the Allow.

Even though the user had permission to use S3, the user could not delete an object because `s3:DeleteObject` was explicitly denied.

## Main takeaway

Explicit Deny overrides Allow.
