# CartBot AI — Session 16 Assignment

## 1. What is the CartBot threat model?

CartBot is an AI-powered e-commerce application. The main actors are the customer and attacker. The application receives requests through its API, processes customer and product data, and sends relevant information to the AI/LLM.

The main trust boundary is between external users/content and the CartBot application. External content should be treated as untrusted.

## 2. What is the CartBot attack chain?

The basic attack chain is:

**Attacker-controlled input → Application trusts the input → Security control is bypassed → Unauthorized behaviour → Business/security impact.**

## 3. What vulnerabilities did you identify?

I identified four main security weaknesses:

* Broken Authentication
* Broken Object Level Authorization (BOLA)
* Indirect Prompt Injection
* Rate-Limiting Failure / Denial of Wallet

## 4. What happened with Broken Authentication?

CartBot trusted a customer ID supplied by the client instead of properly validating the user's identity.

The configuration showed:

`REQUIRE_JWT_VALIDATION = False`

This meant the application was not properly verifying who the user was.

## 5. What happened with BOLA?

The application did not properly check whether a user was authorized to access a particular customer or order.

An attacker could change an object/customer ID and potentially access another customer's information.

The fix was to use server-side authorization and ownership checks.

## 6. What happened with Indirect Prompt Injection?

The AI processed product content that could be controlled by an attacker.

An attacker could place malicious instructions inside product information, which could then enter the AI's context and influence its response.

The main lesson was that external content must be treated as **untrusted data**, not trusted instructions.

## 7. What happened with Rate Limiting / Denial of Wallet?

Rate limiting was disabled:

`RATE_LIMIT_ENABLED = False`

This allowed an attacker to send large numbers of requests. Because AI requests can consume tokens and paid model resources, this could lead to excessive costs.

## 8. What was the impact?

The vulnerabilities could affect:

* **Confidentiality:** unauthorized access to customer information.
* **Integrity:** manipulation of AI responses.
* **Availability:** excessive requests could affect service performance.
* **Financial security:** uncontrolled AI usage could increase costs.

## 9. How were the vulnerabilities remediated?

I strengthened the security controls by:

* Enforcing JWT validation.
* Adding object-level authorization.
* Adding server-side ownership checks.
* Treating external product content as untrusted.
* Adding prompt-injection protections.
* Enabling rate limiting.

The authentication flow now requires the server to validate the token before making an authorization decision.

## 10. How was the fix verified?

I re-ran the security tests after applying the fixes.

**Before remediation:** ❌ 4 FAIL
**After remediation:** ✅ 4 PASS

This provided evidence that the vulnerabilities addressed by the lab's verification tests were remediated.

## 11. Does a Semgrep finding automatically mean the application is exploitable?

No.

Semgrep can identify a suspicious code or configuration pattern, but I still need to investigate the application's behaviour and determine whether the issue is actually exploitable and what the impact would be.

## 12. What did I learn from the assessment?

I learned that finding a vulnerability is only the beginning.

A proper security assessment should:

**Identify → Validate → Understand the impact → Remediate → Retest → Verify**

The important part for me was not just finding what was wrong, but being able to show that the fix actually worked.




