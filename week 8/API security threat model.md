# CartBot AI Lab - Security Threat Model & Remediation Assessment

## 1. API Security Threat Model

| Field                               | Finding                                                                                                                                                                                                                                                                                                                                                             |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Vulnerability 1**                 | **Broken Object Level Authorization (BOLA):** The application trusted client-supplied customer identifiers without adequately validating whether the authenticated user was authorized to access the requested customer object.                                                                                                                                     |
| **OWASP Classification (V1)**       | `API1:2023 – Broken Object Level Authorization`                                                                                                                                                                                                                                                                                                                     |
| **Vulnerability 2**                 | **Indirect Prompt Injection:** Attacker-controlled instructions embedded within product content could be processed by the AI system, potentially influencing model behavior or causing unintended actions.                                                                                                                                                          |
| **MITRE ATLAS Classification (V2)** | `AML.T0051 – LLM Prompt Injection (Indirect)`                                                                                                                                                                                                                                                                                                                       |
| **Vulnerability 3**                 | **Insufficient Rate Limiting / Denial of Wallet:** The absence of effective request throttling allowed automated bulk requests that could consume LLM resources and increase operational costs while potentially enabling large-scale customer-data harvesting.                                                                                                     |
| **MITRE ATLAS Classification (V3)** | `AML.T0054 – LLM Data Exfiltration`                                                                                                                                                                                                                                                                                                                                 |
| **Attack Chain**                    | 1. Attacker supplies malicious input or unauthorized object identifiers.<br>2. Application trusts the supplied input without sufficient server-side validation or authorization enforcement.<br>3. Security boundaries are bypassed.<br>4. Unauthorized behavior executes, potentially resulting in data exposure, excessive LLM consumption, and financial impact. |
| **Business Impact**                 | Potential regulatory and data-privacy exposure, loss of customer trust, unauthorized disclosure of customer information, increased infrastructure/LLM costs, and operational disruption.                                                                                                                                                                            |
| **Root Cause**                      | Security controls were disabled or insufficiently enforced in `api_config.py`, including `REQUIRE_JWT_VALIDATION = False` and `RATE_LIMIT_ENABLED = False`.                                                                                                                                                                                                         |

---

## 2. Remediation

### Authentication & Authorization

The application was modified to enforce **server-side JSON Web Token (JWT) validation** instead of relying on client-supplied customer identifiers.

Object-level authorization checks were also implemented to ensure that authenticated users can only access customer objects they are authorized to access.

### Input Handling & Prompt Injection Mitigation

External seller-controlled product content was treated as **untrusted input**.

The remediation prevents attacker-controlled product content from being blindly interpreted as trusted instructions by the AI system, reducing the risk of indirect prompt injection.

### Rate Limiting & Denial-of-Wallet Protection

Rate limiting was enabled to restrict excessive API requests.

This provides protection against:

* Automated API abuse
* Bulk data harvesting
* Excessive LLM inference
* Uncontrolled token consumption
* Denial-of-Wallet attacks
* Resource exhaustion

---

## 3. Verification Results

The remediated implementation was validated using the reference build's security tests.

| Stage                  | Result   |
| ---------------------- | -------- |
| **Before Remediation** | ❌ 4 FAIL |
| **After Remediation**  | ✅ 4 PASS |

The transition from **4 failing tests to 4 passing tests** demonstrates that the implemented security controls successfully addressed the identified vulnerabilities.

---

## 4. Attack Chain

The identified vulnerabilities can be represented by the following attack chain:

```text
Attacker-Controlled Input
          |
          v
Unauthenticated / Unauthorized Request
          |
          v
Insufficient Server-Side Validation
          |
          v
Security Control Bypass
          |
          +----------------------+
          |                      |
          v                      v
Unauthorized Data Access   Indirect Prompt Injection
          |                      |
          v                      v
Customer Data Exposure    Unintended AI Behavior
          |                      |
          +----------+-----------+
                     |
                     v
              Financial Impact
             / Operational Impact
```

---

## 5. Security Controls Implemented

| Security Control                              | Status        |
| --------------------------------------------- | ------------- |
| Server-side JWT validation                    | ✅ Implemented |
| Object-level authorization                    | ✅ Implemented |
| Client-supplied ID trust removed              | ✅ Remediated  |
| External product content treated as untrusted | ✅ Implemented |
| Indirect prompt injection mitigation          | ✅ Implemented |
| API rate limiting                             | ✅ Enabled     |
| Denial-of-Wallet protection                   | ✅ Implemented |
| Security verification tests                   | ✅ 4/4 PASS    |

---

## 6. Root Cause Analysis

The primary root causes were insecure security configuration and insufficient enforcement of trust boundaries.

The vulnerable configuration included:

```python
REQUIRE_JWT_VALIDATION = False
RATE_LIMIT_ENABLED = False
```

These settings weakened authentication and authorization enforcement and allowed unrestricted API request volumes.

The remediation restored these controls and established stronger server-side security enforcement.

---

## 7. Business Impact

If left unaddressed, the vulnerabilities could have resulted in:

* Unauthorized access to customer information
* Customer data exposure
* Privacy and regulatory compliance issues
* Loss of customer trust
* Automated bulk data harvesting
* Increased LLM/API consumption costs
* Denial-of-Wallet attacks
* Potential service degradation

The implemented controls reduce these risks by enforcing authentication, authorization, trusted-input boundaries, and request-rate controls.

---

## 8. Conclusion

The CartBot AI security assessment identified critical weaknesses across **API authorization, AI input handling, and resource protection**.

The remediation introduced:

1. Server-side JWT validation
2. Object-level authorization
3. Untrusted-input handling for external product content
4. Indirect prompt injection mitigation
5. API rate limiting
6. Denial-of-Wallet protection

The security verification results improved from:

**4 FAIL → 4 PASS**

This demonstrates that the identified security weaknesses were successfully addressed in the remediated implementation.
