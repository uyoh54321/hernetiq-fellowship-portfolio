## Regulatory Exposure

### Finding 1: Unverified External Training Data

| Component | Details |
|---|---|
| **Severity** | High |
| **Finding** | External training data enters the AI pipeline without integrity checks or approval. |
| **Evidence** | `validate_source()` skips hash verification and schema enforcement for external sources. The training manifest records `hash_verified: false` and `schema_enforced: false`. |
| **Business Consequence** | Unverified or manipulated data could degrade model performance and compromise AI reliability. |
| **Regulatory Exposure** | EU AI Act — Article 10(2), (3): Data governance, data quality, and bias examination requirements for high-risk AI systems. |
| **Recommended Action** | Engineering should implement mandatory hash verification, schema validation, and external data approval before ingestion. |

### Finding 2: Automated Deployment Without Human Approval

| Component | Details |
|---|---|
| **Severity** | High |
| **Finding** | AI models are deployed to production automatically without human review or sign-off. |
| **Evidence** | `_deploy_model()` executes automatically upon pipeline completion, with no human review gate. |
| **Business Consequence** | Defective or unsafe models could enter production without approval, increasing operational and compliance risks. |
| **Regulatory Exposure** | EU AI Act — Article 14(1), (4): Human oversight requirements for high-risk AI systems. |
| **Recommended Action** | The CTO should introduce mandatory human approval before deployment, with documented sign-off and rollback procedures. |

### Finding 3: Missing Bias Review Before Deployment

| Component | Details |
|---|---|
| **Severity** | High |
| **Finding** | Training data is used for model deployment without completing a bias review. |
| **Evidence** | The training manifest records `bias_review_completed: false`. The deployment pipeline proceeds without a bias review gate. |
| **Business Consequence** | Biased model outputs could affect users, undermine trust, and expose the organization to regulatory scrutiny. |
| **Regulatory Exposure** | EU AI Act — Article 10(2), (3): Data governance and examination requirements addressing bias for high-risk AI systems. |
| **Recommended Action** | Engineering and Compliance should establish mandatory bias testing and documented approval before model deployment. |
