#  AI Application Security Assessment Report

## CartBot AI — Security Assessment & Remediation

**Program:** HerNetIQ AI Security Fellowship
**Lab:** CartBot AI
**Assessment Type:** AI Application & API Security Assessment
**Status:** ✅ Remediated and Verified

---

## 1. Executive Summary

As part of my **HerNetIQ AI Security Fellowship**, I conducted a security assessment of **CartBot AI**, a simulated AI-powered e-commerce API.

The objective was not only to identify vulnerabilities, but also to understand how they could be exploited, implement appropriate security controls, and verify that the remediation actually worked.

The assessment identified three key vulnerabilities:

1. **Broken Object Level Authorization (BOLA)**
2. **Indirect Prompt Injection**
3. **Insufficient Rate Limiting / Denial of Wallet**

The vulnerabilities exposed risks involving unauthorized data access, manipulation of AI behavior, automated data harvesting, and uncontrolled LLM resource consumption.

Following remediation, security verification tests improved from:

> ❌ **4 FAIL → ✅ 4 PASS**

This provided evidence that the implemented security controls addressed the identified issues in the lab environment.

---

# 2. Assessment Objectives

The assessment focused on:

* Testing authentication and authorization controls
* Identifying object-level authorization weaknesses
* Testing the application's handling of attacker-controlled content
* Assessing indirect prompt injection risks
* Evaluating API rate-limiting controls
* Assessing potential LLM resource and cost abuse
* Implementing security remediation
* Re-running security tests to verify the fixes

---

# 3. Scope

The assessment covered the application's:

* Customer-related API endpoints
* Product-related API functionality
* Authentication and authorization mechanisms
* AI/LLM processing workflow
* External product content
* API request controls
* Security configuration
* Automated security verification tests

---

# 4. Vulnerability Findings

## V1 — Broken Object Level Authorization (BOLA)

**Severity:** High

**Classification:** `OWASP API1:2023 – Broken Object Level Authorization`

### Description

The API trusted client-supplied customer identifiers without properly verifying whether the requesting user was authorized to access the referenced customer object.

This created a classic **BOLA** vulnerability.

An attacker could potentially modify a customer identifier in a request and access information belonging to another customer.

### Attack Scenario

```text
Authenticated User
        |
        v
Customer Request
/customer/1001
        |
        v
Modify Object ID
/customer/1002
        |
        v
Server fails to verify ownership
        |
        v
Unauthorized Customer Data Access
```

### Root Cause

The application relied on client-supplied identifiers without sufficient server-side authorization enforcement.

The security configuration also contained:

```python
REQUIRE_JWT_VALIDATION = False
```

### Impact

Successful exploitation could result in:

* Unauthorized customer-data access
* Privacy violations
* Data exposure
* Regulatory risk
* Loss of customer trust

### Remediation

I implemented:

* Server-side JWT validation
* Object-level authorization
* Server-side ownership checks
* Authentication enforcement

The application now verifies that the authenticated user is authorized to access the requested object.

---

# V2 — Indirect Prompt Injection

**Severity:** High

**Classification:** `MITRE ATLAS AML.T0051 – LLM Prompt Injection (Indirect)`

### Description

The application processed product content that could be controlled by an attacker.

Because this content was passed into the AI workflow without sufficient separation between **trusted instructions and untrusted data**, an attacker could embed malicious instructions within product content.

The AI could then interpret the attacker-controlled content as instructions rather than simply as product data.

### Attack Scenario

```text
Attacker
   |
   v
Malicious Product Description
   |
   v
E-Commerce API
   |
   v
AI / LLM Processing
   |
   v
Indirect Prompt Injection
   |
   v
Manipulated AI Behaviour
```

### Impact

Potential consequences included:

* Manipulation of AI responses
* Unintended AI behavior
* Potential information disclosure
* Circumvention of intended AI instructions
* Increased downstream security risk

### Root Cause

The application did not sufficiently establish a trust boundary between external content and trusted AI instructions.

### Remediation

I treated externally controlled product content as **untrusted input** and implemented input-handling and prompt-injection mitigation controls.

This reduces the likelihood that attacker-controlled product information will be interpreted as trusted instructions by the AI system.

---

# V3 — Insufficient Rate Limiting / Denial of Wallet

**Severity:** High

**Classification:** `MITRE ATLAS AML.T0054 – LLM Data Exfiltration`

### Description

The application lacked effective rate limiting, allowing automated requests to be submitted at high volumes.

In an AI-powered application, unrestricted requests create more than an availability problem.

Repeated requests can trigger additional LLM inference, resulting in increased:

* Token consumption
* Compute usage
* API usage
* Infrastructure costs

This creates a potential **Denial-of-Wallet** risk.

### Root Cause

Rate limiting was disabled in the application configuration:

```python
RATE_LIMIT_ENABLED = False
```

### Attack Scenario

```text
Attacker
   |
   v
Automated Requests
   |
   v
API
   |
   v
LLM Inference
   |
   +------> Data Harvesting
   |
   +------> Token Consumption
   |
   +------> Increased Costs
   |
   v
Potential Service Degradation
```

### Impact

Potential consequences included:

* Automated bulk harvesting
* Excessive LLM consumption
* Increased operational costs
* Resource exhaustion
* Service degradation
* Financial impact

### Remediation

I enabled request throttling to restrict excessive API traffic.

This provides protection against both **API abuse** and **uncontrolled LLM consumption**.

---

# 5. Attack Chain

The vulnerabilities demonstrate how weaknesses in different layers of an AI application can combine to increase overall risk.

```text
Attacker-Controlled Input
          |
          v
Insufficient Validation
          |
          v
Security Boundary Bypass
          |
     +----+----+
     |         |
     v         v
   BOLA    Prompt Injection
     |         |
     v         v
Data Access  AI Manipulation
     |         |
     +----+----+
          |
          v
     Automated Abuse
          |
          v
   Excessive Requests
          |
          v
   LLM Consumption
          |
          v
Financial / Operational Impact
```

---

# 6. Remediation Summary

| Vulnerability                    | Remediation                                            | Status  |
| -------------------------------- | ------------------------------------------------------ | ------- |
| BOLA                             | JWT validation + object-level authorization            | ✅ Fixed |
| Indirect Prompt Injection        | Untrusted-input handling + prompt injection mitigation | ✅ Fixed |
| Rate Limiting / Denial of Wallet | Request throttling                                     | ✅ Fixed |

---

# 7. Security Controls Implemented

### Authentication

* Enforced server-side JWT validation
* Removed reliance on unauthenticated client-supplied identity

### Authorization

* Implemented object-level authorization
* Added server-side ownership checks

### AI Security

* Treated external product content as untrusted
* Added controls against indirect prompt injection
* Established a stronger separation between data and instructions

### API Security

* Enabled rate limiting
* Restricted excessive request volumes
* Reduced opportunities for automated abuse

### Verification

* Re-ran security tests after remediation
* Confirmed that previously failing tests passed

---

# 8. Verification Results

Security testing was performed before and after remediation.

### Before Remediation

```text
4 FAIL
```

### After Remediation

```text
4 PASS
```

| Stage              | Passed | Failed | Result |
| ------------------ | -----: | -----: | ------ |
| Before remediation |      0 |      4 | ❌ FAIL |
| After remediation  |      4 |      0 | ✅ PASS |

The successful transition from **4 FAIL to 4 PASS** provides evidence that the implemented controls addressed the identified vulnerabilities.

---

# 9. Business Impact

If left unaddressed, these vulnerabilities could have resulted in:

### Confidentiality

Unauthorized access to customer information through BOLA.

### Integrity

Manipulation of AI behavior through indirect prompt injection.

### Availability

Excessive automated requests potentially affecting application availability.

### Financial Impact

Uncontrolled LLM inference potentially increasing token and infrastructure costs.

### Regulatory Impact

Unauthorized access to customer information could create data-protection and privacy concerns.

---

# 10. Root Cause

The primary root cause was insufficient enforcement of security controls at application boundaries.

In particular, security configuration included disabled controls such as:

```python
REQUIRE_JWT_VALIDATION = False
RATE_LIMIT_ENABLED = False
```

These settings weakened authentication enforcement and request-abuse protection.

The remediation restored these controls and strengthened server-side security enforcement.

---

# 11. Lessons Learned

This lab reinforced an important principle:

**AI application security is not only about securing the LLM.**

The surrounding application matters just as much.

An AI application can have a powerful model but still be vulnerable because of:

* Weak authentication
* Broken authorization
* Unsafe input handling
* Poor API controls
* Excessive trust in external content
* Lack of resource protection

I also learned that remediation should not end when the code changes.

The complete security cycle is:

```text
Identify
   ↓
Understand
   ↓
Test / Validate
   ↓
Remediate
   ↓
Retest
   ↓
Verify
```

> **Changed code proves nothing. Evidence does.**

---

# 12. Conclusion

The CartBot AI assessment provided practical experience in securing an application at the intersection of **API security and AI security**.

I identified and remediated:

* Broken Object Level Authorization
* Indirect Prompt Injection
* Insufficient Rate Limiting / Denial of Wallet

The remediation introduced stronger authentication, authorization, input-handling, AI security, and API-abuse controls.

Most importantly, the verification results demonstrated measurable improvement:

## ❌ 4 FAIL → ✅ 4 PASS

This lab reinforced for me that security work is not simply about finding what is broken.

It is about **understanding the attack path, fixing the root cause, and producing evidence that the fix works.**

---

# 13. Evidence

All implementation and verification evidence is available in:
https://github.com/uyoh54321/hernetiq-fellowship-portfolio/commit/ca35ce4f348db7c194af6f95dc8fec00658af4bd

```

The evidence includes the relevant implementation, configuration changes, security tests, and verification results.

---

## Security Assessment Status

**Assessment:** Complete
**Remediation:** Complete
**Verification:** Passed
**Final Result:** ✅ **4/4 Security Tests Passing**

**Changed code proves nothing. Evidence does.**
