As part of the HerNetIQ AI Security Fellowship, I recently completed Level 1 of the AI Defense Lab: MedVitals AI — Cloud Infrastructure Security.

This was a hands-on exercise where I investigated a simulated security incident, found what went wrong, followed the evidence, and fixed the vulnerabilities.

The Scenario
MedVitals AI is a HealthTech company that provides an AI-powered triage service for patients.

The problem started when a developer placed live AWS credentials directly inside the application code and pushed the code to a public repository.

To make things worse, the account connected to those credentials had very broad permissions. In simple terms, the credentials had far more access than they actually needed.

An attacker was then able to use those credentials to access the cloud environment, look for storage resources, and interact with patient-record data.



Setting Up the Lab

I started by creating my own copy of the lab repository, deploying it to Streamlit, and completing the setup.

Once inside the lab, Level 1 was available while Level 2 remained locked.

The objective was straightforward:

Find the security weaknesses.
Investigate what happened.
Fix the problems.
Document the evidence.
Finding the Vulnerabilities
I first examined two files in the deployment repository:

config.py and deploy-role-policy.json.

1. Exposed Credentials
The first problem was that sensitive AWS credentials had been placed directly in the application code.

Think of this like writing the key to your house on a public noticeboard. Anyone who finds it could potentially use it.

2. Too Much Access
The second problem was the IAM policy.

It used wildcard permissions, essentially saying:

“This account can do everything.”

That is a major security risk. A better approach is least privilege — give an account only the access it actually needs.

Following the Evidence
Next, I examined the 17 CloudTrail events.

CloudTrail is essentially a record of activity within AWS. It helped me answer questions such as:

Who performed the action?
What did they do?
When did they do it?
Where did the request come from?
How was the request made?
The first major suspicious event occurred at 03:02:09 UTC on June 30, 2026.





The medvitals-deploy-bot identity successfully assumed the AdminFullAccess role from the source IP 198.51.100.45.



Cloud trail event history
The activity that followed was:

AssumeRole → ListBuckets → PutObject → GetObject

In simple terms, the account gained powerful access, looked through the available storage, wrote an object, and then retrieved an object.



Expanded assume role event
This was the point where the investigation moved from simply identifying vulnerabilities to understanding how they could be used.

Fixing the Problems
After completing the investigation, I worked on the remediation.

Removing the credentials from the code
I removed the hardcoded credentials and changed the application to retrieve sensitive values through environment variables instead.

I also added .env to .gitignore so that environment files are not accidentally committed to the repository.

Reducing unnecessary access
I also replaced the overly broad IAM permissions with a least-privilege approach, limiting access to what the application actually requires.

The goal is simple:

If an account is compromised, limit what the attacker can do with it.

The Commit
I committed my changes to GitHub.

Commit: a6575fe

Commit message:

Level 1 fix: removed hardcoded credentials, least privilege IAM policy, secrets governance

The commit contained changes to two files, with 44 additions and 10 deletions.





Level 1 fix: removed hardcoded credentials, least privilege IAM polic... ·…
Multi-sector defensive sandbox simulating Cloud, RAG, and autonomous agent vulnerabilities for the AI Security…
github.com

What I Learned
This lab helped me understand that cloud security isn’t only about complicated technical tools.

Sometimes, a serious security problem can start with something as simple as putting a password or access key in the wrong place.

I also learned the importance of least privilege. Giving an account only the access it needs can greatly reduce the damage if that account is ever compromised.

Most importantly, I learned that investigating a security incident is about following the evidence, understanding what happened, fixing the root cause, and documenting the work clearly.

Level 1 complete. Level 2 unlocked.

On to the next challenge.
