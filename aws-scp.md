# 🚧 AWS Control Tower - Service Control Policies (SCPs)

## 📘 What are SCPs?

- **SCPs ≠ IAM policies**: SCPs limit what permissions **can be granted** by IAM.
- Applied at the **Organizational Unit (OU)** level.
- Enforced **globally** on all AWS accounts within an OU.

## ✅ Use Cases (Production-Ready Examples)

### 🔒 Deny Access to Non-Approved Regions

Restrict access to only approved AWS regions such as `us-east-1` and `us-west-2`.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyRegions",
      "Effect": "Deny",
      "Action": "*",
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:RequestedRegion": ["us-east-1", "us-west-2"]
        }
      }
    }
  ]
}

```

### 🔒 Deny Deletion or Modification of Logging Services


```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyCloudTrailDelete",
      "Effect": "Deny",
      "Action": [
        "cloudtrail:DeleteTrail",
        "cloudtrail:StopLogging",
        "config:DeleteConfigurationRecorder"
      ],
      "Resource": "*"
    }
  ]
}

```

### 🔒 Allow Only Approved Services

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowApprovedServicesOnly",
      "Effect": "Deny",
      "NotAction": [
        "ec2:*",
        "s3:*",
        "lambda:*"
      ],
      "Resource": "*"
    }
  ]
}


```

### 🔒 Deny Specific Actions Globally

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenySensitiveActions",
      "Effect": "Deny",
      "Action": [
        "iam:CreateUser",
        "kms:ScheduleKeyDeletion",
        "organizations:LeaveOrganization"
      ],
      "Resource": "*"
    }
  ]
}

```

```

### 🔒 Enforce Mandatory Resource Tagging
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "RequireTags",
      "Effect": "Deny",
      "Action": "*",
      "Resource": "*",
      "Condition": {
        "Null": {
          "aws:RequestTag/Project": "true"
        }
      }
    }
  ]
}

```
