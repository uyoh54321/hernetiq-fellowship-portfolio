# Agent Governance & Defense Report: LegalBot Municipal

**Frameworks:** MAESTRO 7-Layer Threat Model | NIST AI RMF

## 1. Executive Summary

LegalBot Municipal faces a potential indirect prompt-injection risk through untrusted citizen comments. Excessive agent permissions could allow unauthorized legal actions without human approval.

## 2. MAESTRO Threat Model

| Layer | Finding / Mitigation |
|---|---|
| L1: Foundation Models | Prompt injection risk; separate trusted instructions from user input. |
| L2: Data Operations | Untrusted comments; validate and isolate input. |
| L3: Agent Frameworks | Excessive agency; restrict high-impact tool permissions. |
| L4: Infrastructure | Missing authorization controls; enforce server-side checks. |
| L5: Observability | Insufficient monitoring; log and test tool calls. |
| L6: Security & Compliance | Weak oversight; require human approval. |
| L7: Agent Ecosystem | Excessive API access; enforce scoped permissions. |

## 3. NIST AI RMF Mapping

- **GOVERN:** Establish policies, accountability, and least-privilege access.
- **MAP:** Identify prompt-injection risks and potential legal and operational impacts.
- **MEASURE:** Test authorization boundaries, monitor tool calls, and audit security controls.
- **MANAGE:** Revoke excessive permissions, require human approval, and update incident response procedures.

## 4. OWASP Classification

- **ASI01 — Agent Goal Hijack:** Malicious input redirects agent objectives.
- **ASI02 — Tool Misuse:** Tools are used in unsafe or unauthorized ways.
- **ASI03 — Identity and Privilege Abuse:** Relevant if identity or privilege weaknesses are confirmed.

## 5. Root Cause & Business Impact

**Root Cause:** Inadequate authorization controls and excessive agent permissions.

**Potential Impact:** Unauthorized legal actions, financial losses, legal liability, and reputational damage.

## 6. Remediation & Verification

1. Remove unnecessary permissions from `levels/level5_legalbot.py`.
2. Enforce server-side authorization and human approval for high-impact actions.
3. Run syntax and static analysis checks:
   ```bash
   python3 -m py_compile levels/level5_legalbot.py
   semgrep scan --config auto levels/level5_legalbot.py
   ```
4. Test malicious inputs and unauthorized tool calls; verify that actions are blocked and logged.

**Success Criterion:** No high-impact action executes without required authorization and human approval.

## Conclusion

MAESTRO identifies architectural threats, while NIST AI RMF guides governance, risk assessment, testing, and remediation. Together, they help keep AI agents within authorized boundaries.
