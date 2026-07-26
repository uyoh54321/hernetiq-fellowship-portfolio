

1)INCIDENT SUMMARY.  
on the 10th july,an attacker used a compromised backup user account to access the AWS environment,
he verified the credentials,created a new IAM user and granted it admin privilege to maintain longterm access

2)ATTACK TIMELINE


01:12:44 UTC – GetCallerIdentity: Verified the stolen AWS credentials.

01:14:09 UTC – DescribeInstances: Explored the AWS environment by listing EC2 instances.

01:19:31 UTC – CreateUser: Created a new IAM user (medvitals-support-svc).

01:26:58 UTC – AttachUserPolicy: Granted the new user AdministratorAccess, giving it full control.




3) ROOT CAUSE ANALYSIS
The medvitals-backup-user account had excessive IAM permissions, including iam:CreateUser and iam:AttachUserPolicy,
allowing the attacker to create a new administrator account.

4)HARDENED IAM POLICY.

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket",
        "rds:DescribeDBInstances",
        "rds:Connect"
      ],
      "Resource": [
        "arn:aws:s3:::medvitals-patient-docs",
        "arn:aws:rds:eu-west-1:123456789:db:medvitals-prod"
      ]
    }
  ]
}

5. RECOMMENDATIONS
Disable the compromised account and remove the unauthorized IAM user.
Rotate credentials and enable MFA.
Apply least privilege to IAM accounts.
Monitor CloudTrail for suspicious IAM activities.
Conduct regular IAM permission reviews.
