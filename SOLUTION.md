# IAM Roles for EC2 Lab - Solution

**Student Name:** Adnan Nooruddin  
**Date Completed:** 16.07.2026

---

# Environment Details

| Item | Value |
|------|-------|
| Instance ID | [i-098f9b3723aa41153] |
| Region | [eu-west-1] |
| S3 Bucket Name | [ce-bootcamp-m2-05-adnan] |
| Policy Name | [ec2-s3-access-policy] |
| Role Name | [ec2-s3-cloudwatch-role] |

---

# Step 1: Create an S3 Bucket

- [x] Bucket created with `aws s3 mb`
- [x] Bucket appears in `aws s3 ls`

**My bucket name:** `ce-bootcamp-m2-05-adnan`

---

# Step 2 & 3: Create the Custom Policy

- [x] Policy `ec2-s3-access-policy` created from the JSON
- [x] Both `YOUR-BUCKET-NAME` placeholders replaced with my real bucket
- [x] `Resource` has **both** ARNs (bucket and objects)

### Why does the policy need two ARNs (one with `/*`, one without)?

```
arn:aws:s3:::YOUR-BUCKET-NAME       <- the bucket   -> s3:ListBucket
arn:aws:s3:::YOUR-BUCKET-NAME/*     <- the objects  -> s3:GetObject, s3:PutObject

ListBucket is an operation performed on the bucket. GetObject and PutObject are performed on objects. These are different resource types and require different ARNs.

If the bare bucket ARN is omitted, uploads succeed but aws s3 ls fails with AccessDenied
```

---

# Step 4: Create the IAM Role

## Screenshot 1 – Role Creation

```
screenshots/01-role-creation.png
```

![Role Creation](screenshots/01-role-creation.png)

## Screenshot 2 – Policy Attachment

```
screenshots/02-policy-attachment.png
```

![Policy Attachment](screenshots/02-policy-attachment.png)

---

- [x] Role `ec2-s3-cloudwatch-role` created with **EC2** trusted entity
- [x] `CloudWatchAgentServerPolicy` attached
- [x] `ec2-s3-access-policy` attached

---

# Step 5: Attach the Role to Your Instance

## Screenshot 3 – EC2 with Role

```
screenshots/03-ec2-with-role.png
```

![EC2 with Role](screenshots/03-ec2-with-role.png)

---

- [x] Role attached via **Actions → Security → Modify IAM role**
- [x] Instance **Details** tab shows the IAM role

---

# Step 6: Confirm No Credentials Exist on the Instance

- [x] Ran `ls -la ~/.aws/` on the instance
- [x] No `~/.aws/credentials` file present (deleted it if it existed)

---

# Step 7: Test the Role

## Screenshot 4 – Assumed-Role Identity

```
screenshots/04-assumed-role-identity.png
```

![Assumed Role Identity](screenshots/04-assumed-role-identity.png)

## Screenshot 5 – S3 Upload Success

```
screenshots/05-s3-upload-success.png
```

![S3 Upload Success](screenshots/05-s3-upload-success.png)

---

- [x] `aws sts get-caller-identity` shows `assumed-role/`, not `user`
- [x] `aws s3 ls s3://YOUR-BUCKET-NAME/` works
- [x] Upload (`aws s3 cp test.txt ...`) works
- [x] Read-back works
- [x] I never typed a credential

### The `Arn` from `get-caller-identity`

```text
arn:aws:sts::128529977749:assumed-role/ec2-s3-cloudwatch-role/i-098f9b3723aa41153
```

---

# Step 8: Test Least Privilege

## Screenshot 6 – Access Denied Proof

```
screenshots/06-access-denied-proof.png
```

![Access Denied Proof](screenshots/06-access-denied-proof.png)

---

- [x] Listing a bucket I was not granted → `AccessDenied`
- [x] `aws s3 rb` (delete, not granted) → `AccessDenied`

---

# Step 9: Capture the Trust Policy

- [x] Ran `aws iam get-role ...` **from my laptop** (not the instance)
- [x] Saved output as `trust-policy.json`
- [x] Trust policy `Principal` is `ec2.amazonaws.com`

---

# Step 10: Locate the Source of the Credentials

- [x] Fetched an IMDSv2 token, then read the role credentials from `169.254.169.254`
- [x] Response includes `AccessKeyId`, `SecretAccessKey`, `Token`, and an `Expiration`

```
_______________________________________________________________
```

---

# Cleanup

- [x] Emptied and deleted the S3 bucket (`aws s3 rb ... --force`)
- [x] Instance **stopped** (not terminated)
- [x] IAM role left in place (costs nothing)

---

# Submission Checklist

Repository name: `ce-lab-iam-roles-ec2` (**public**)

- [x] `policies/s3-cloudwatch-policy.json` and `policies/trust-policy.json` committed
- [ ] `test-output/` files committed (commands, S3 test, access-denied test)
- [x] All 6 screenshots present
- [x] `README.md` complete with reflections
- [x] Policy uses **both** ARN forms
- [x] `get-caller-identity` shows `assumed-role/`
- [x] `~/.aws/credentials` does **not** exist on the instance
- [x] Account ID redacted (if I chose to)
- [x] Repository URL submitted
