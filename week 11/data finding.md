# PayGuard AI — STRIDE Threat Model & Security Findings Report

This report presents the security assessment and threat model for **PayGuard AI**. It maps the vulnerabilities identified during the vector retrieval and embedding security labs to the **STRIDE threat-modeling framework** and relevant **OWASP LLM security classifications**.

## STRIDE Threat Model

| STRIDE Category            | Security Finding / Description                                                                                                                                                                                                                                                 |
| :------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Spoofing**               | Client-controlled `tenant_id` parameters can be manipulated to impersonate another tenant or client session. For example, changing the value from `meridian` to `alpine` or `solstice` may allow unauthorized access to another tenant's resources.                            |
| **Tampering**              | Shared vector index structures may be manipulated, while malicious or compromised embedding-model publishers could intentionally influence how queries are clustered and retrieved, potentially directing users toward sensitive or attacker-controlled content.               |
| **Repudiation**            | Inadequate audit logging and insufficiently traceable request records make it difficult to attribute and investigate cross-tenant retrieval activity, reducing accountability and forensic visibility.                                                                         |
| **Information Disclosure** | Missing tenant isolation can result in unauthorized retrieval of confidential client documents, including partial SSNs and M&A briefs. Exposed embedding vectors may also be vulnerable to inversion attacks that attempt to reconstruct information from the underlying data. |
| **Denial of Service**      | An unthrottled bulk-query vulnerability creates a **Denial-of-Wallet** risk. Automated harvesting scripts can consume excessive vector-database resources, increasing compute usage and cloud-query costs.                                                                     |
| **Elevation of Privilege** | Weak tenant-level access controls may allow users to move laterally across tenant boundaries, giving standard users access to isolated or privileged client namespaces that should be inaccessible to them.                                                                    |

---

## Additional Security Classification & Analysis

### OWASP Classification

**LLM09: Vector and Embedding Weaknesses**, with associated risks involving **Sensitive Data Exposure** and inadequate tenant isolation.

### Business Impact

The identified vulnerabilities could result in:

* Exposure of personally identifiable information (PII) and sensitive financial data.
* Regulatory and legal penalties arising from data-protection violations.
* Loss of client confidentiality and trust.
* Reputational damage to PayGuard AI.
* Unauthorized access to information belonging to other tenants.
* Increased infrastructure and cloud-query costs resulting from Denial-of-Wallet attacks.

### Root Cause

The primary root causes identified are:

1. A shared vector-index architecture without sufficiently enforced per-tenant security boundaries.
2. Retrieval queries that do not consistently enforce tenant-level metadata filtering.
3. Reliance on client-supplied parameters such as `tenant_id` to determine authorization context.
4. Insufficient API rate limiting and query throttling.
5. Exposure of raw embedding vectors without adequate access controls.

### Recommended Remediation

1. **Enforce tenant isolation at the authorization layer.**
   Apply mandatory server-side tenant controls to every retrieval request and prevent users from overriding their authorized tenant context.

2. **Implement strict metadata filtering.**
   Enforce per-tenant metadata filters on all vector-database queries and validate that filters cannot be removed or modified by clients.

3. **Secure the tenant context.**
   Derive the tenant identity from the authenticated server-side session or authorization token rather than trusting client-supplied `tenant_id` parameters.

4. **Implement rate limiting and query throttling.**
   Apply appropriate request limits, quotas, and anomaly detection to prevent automated bulk harvesting and reduce Denial-of-Wallet exposure.

5. **Protect raw embedding vectors.**
   Restrict direct access to embedding vectors and apply access controls comparable to those protecting the original source documents.

6. **Strengthen audit logging.**
   Log authentication context, tenant identity, retrieval requests, authorization decisions, and anomalous cross-tenant access attempts to support detection and forensic investigation.
