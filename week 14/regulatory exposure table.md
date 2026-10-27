AI Compliance Gap Analysis Report

Assessment: HerNetIQ S28/S29
Overall Risk: HIGH

1. Executive Summary

The assessment identified three gaps: unverified external training data, missing bias reviews, and automated model deployment without human approval. These weaknesses may expose the organization to model reliability, discrimination, and regulatory risks.

2. Key Findings

1. Unverified Training Data: External data bypasses integrity and schema checks.
2. Missing Bias Review: The external dataset has no completed bias review.
3. Automated Deployment: Models enter production without documented human sign-off.

3. Regulatory Exposure Table

Identified Gap	Regulation / Framework	Article / Reference	Potential Exposure
Unverified external training data	EU AI Act — Regulation (EU) 2024/1689	Article 10(2)(b), 10(2)(f), and 10(3)	Inadequate data governance, bias mitigation, and data quality for high-risk AI systems.
Missing bias review	EU AI Act — Regulation (EU) 2024/1689	Article 10(2)(f) and 10(3)	Failure to identify and mitigate relevant data biases affecting high-risk AI systems.
Deployment without documented human oversight	EU AI Act — Regulation (EU) 2024/1689	Article 14(1) and 14(4); Article 43	Potentially inadequate human oversight and failure to complete the required conformity assessment before deployment of a high-risk system.
Automated decisions using personal data (conditional)	General Data Protection Regulation (GDPR)	Article 22	Additional exposure if the system makes solely automated decisions with legal or similarly significant effects and no applicable exception or safeguard applies.

Applicability note: These are potential exposures, not confirmed legal violations. EU AI Act requirements depend on the system’s classification, intended use, and applicable scope. GDPR Article 22 applies only where its specific conditions are met.

Official references: EU AI Act — Regulation (EU) 2024/1689 · GDPR — Regulation (EU) 2016/679

4. Recommended Actions

Action	Owner	Deadline
Enforce external-data integrity, schema, and approval checks	CTO	30 days
Require documented bias reviews before training	Compliance Lead	30 days
Implement appropriate human oversight and deployment approval controls	CTO	30 days
Confirm legal applicability and retain remediation evidence	Compliance Lead	45 days

5. Conclusion

Prioritize the three gaps, verify applicable regulatory obligations, and retain evidence of corrective actions for audit and governance review.
