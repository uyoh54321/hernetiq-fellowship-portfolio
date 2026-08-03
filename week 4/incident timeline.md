# Incident Timeline Report

## MedVitals AI – Cloud Infrastructure Security Incident

## Overview

This report documents the investigation of a security incident within the MedVitals AI AWS environment. The investigation identified two critical security vulnerabilities: hardcoded credentials in the application configuration and an overly permissive IAM policy. These weaknesses enabled an attacker to gain unauthorized access, perform reconnaissance, escalate privileges, modify cloud resources, and access data stored in Amazon S3.

---

# The Five Ws

## Who

The attacker gained access to the MedVitals AWS environment using exposed AWS credentials embedded in the application configuration. Once authenticated, the attacker used the compromised identity to interact with multiple AWS services.

## What

The attacker performed reconnaissance, inspected security configurations, enumerated IAM roles, modified an AWS Lambda function, assumed an administrative IAM role, and accessed Amazon S3 resources.

## When

**Date:** 29–30 June 2026 (UTC)

## Where

The incident occurred within the MedVitals AWS cloud environment and involved the following AWS services:

- AWS IAM
- Amazon EC2
- Amazon S3
- AWS Lambda
- Amazon CloudWatch Logs

## Why

The compromise was made possible by two security vulnerabilities:

1. **Hardcoded Secrets** stored directly in `config.py`.
2. **Overly Permissive IAM Policy** configured in `deploy-role-policy.json`, granting unrestricted permissions (`"Action": "*"` and `"Resource": "*"`), which violated the Principle of Least Privilege.

---

# Identified Vulnerabilities

## Vulnerability 1 – Hardcoded Secrets

**Location:** `config.py`

The application stored sensitive credentials directly within the source code, including:

- AWS Secret Access Key
- Database Password
- Session Secret

Hardcoding sensitive credentials exposes them to anyone with access to the source code and significantly increases the risk of unauthorized access.

---

## Vulnerability 2 – Overly Permissive IAM Policy

**Location:** `deploy-role-policy.json`

The IAM policy granted unrestricted permissions:

```json
"Action": "*",
"Resource": "*"
```

This configuration allowed the compromised identity to perform virtually any action within the AWS account, including privilege escalation and modification of cloud resources.

---

# Attack Timeline

| Time (UTC) | CloudTrail Event | Description |
|------------|------------------|-------------|
| 2026-06-29 23:14:09 | DescribeSecurityGroups | The attacker began reconnaissance by examining network security group configurations. |
| 2026-06-29 23:47:33 | GetBucketPolicy | The attacker inspected Amazon S3 bucket policies to determine available storage permissions. |
| 2026-06-30 00:12:55 | PutBucketLogging | The attacker modified S3 bucket logging settings. |
| 2026-06-30 00:51:17 | DescribeInstances | The attacker enumerated EC2 instances to understand the cloud infrastructure. |
| 2026-06-30 02:05:19 | ListRoles | The attacker enumerated IAM roles to identify privileged identities. |
| 2026-06-30 02:14:11 | ConsoleLogin | The attacker successfully authenticated to the AWS Management Console. |
| 2026-06-30 02:14:45 | DescribeInstances | Additional reconnaissance of EC2 resources was performed after console access. |
| 2026-06-30 02:38:04 | GetBucketAcl | The attacker examined Amazon S3 bucket access control lists. |
| 2026-06-30 02:55:33 | UpdateFunctionCode | The attacker modified an AWS Lambda function, indicating unauthorized code changes. |
| 2026-06-30 03:02:09 | AssumeRole | The attacker assumed the **AdminFullAccess** IAM role, successfully escalating privileges. |
| 2026-06-30 03:02:31 | ListBuckets | Using elevated privileges, the attacker enumerated Amazon S3 buckets. |
| 2026-06-30 03:03:02 | PutObject | The attacker uploaded or modified data within an Amazon S3 bucket. |
| 2026-06-30 03:04:18 | GetObject | The attacker accessed objects stored within Amazon S3. |

---

# Noise Events

The following CloudTrail events were reviewed but excluded from the primary attack timeline because they appear to represent routine application or infrastructure activity rather than malicious actions.

| Event | Reason for Exclusion |
|-------|----------------------|
| GetObject (23:01:44) | Normal application access to Amazon S3. |
| GetObject (01:03:28) | Routine application activity. |
| CreateLogGroup | Standard CloudWatch log creation. |
| DescribeLogStreams | Routine CloudWatch logging operation. |

---

# Remediation

The following corrective actions were implemented:

- Removed all hardcoded credentials from `config.py`.
- Stored sensitive credentials securely using environment variables.
- Replaced the wildcard IAM permissions with least-privilege permissions required by the application.
- Reviewed IAM roles and permissions to ensure compliance with the Principle of Least Privilege.
- Rotated exposed AWS credentials to prevent further unauthorized access.

---

# What the Fix Does

The implemented fixes prevent sensitive credentials from being exposed in the application source code and significantly reduce the permissions granted to AWS identities. These changes limit the potential impact of compromised credentials and prevent attackers from performing unrestricted actions or escalating privileges within the AWS environment.

---

# Business Impact

If this incident had occurred in a production healthcare environment, the attacker could have obtained administrative access to cloud resources, modified application code, accessed sensitive patient information, and disrupted critical healthcare services. Such an incident could lead to regulatory penalties, reputational damage, operational disruption, and reduced stakeholder confidence during MedVitals AI's funding activities.

---

# Conclusion

The investigation concluded that the security incident resulted from a combination of hardcoded AWS credentials and an overly permissive IAM policy. These weaknesses enabled the attacker to perform reconnaissance, escalate privileges through an administrative IAM role, modify cloud resources, and access Amazon S3 data. Implementing secure secrets management, least-privilege IAM policies, continuous monitoring, and regular security reviews significantly reduces the likelihood of similar incidents in the future.
