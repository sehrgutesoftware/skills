# OWASP Top 10:2025

Categorization reference for the Security Audit skill. Map every finding to one of these categories in the report.

Source: https://owasp.org/Top10/2025/ (snapshot: 2026-09-30)

## Categories

### A01:2025 — Broken Access Control
Failures to enforce that users can only act within their intended permissions. Includes IDOR, missing function-level checks, forced browsing, privilege escalation, and metadata manipulation (JWT, cookies, hidden fields).

### A02:2025 — Security Misconfiguration
Insecure defaults, incomplete configurations, open cloud storage, verbose error messages, unpatched flaws, unnecessary features enabled, missing security headers. Includes XML External Entities (XXE) as a misconfiguration class.

### A03:2025 — Software Supply Chain Failures
Compromise via dependencies, build tooling, package registries, or unsigned artifacts. Includes vulnerable third-party components, dependency confusion, malicious packages, and untrusted CI/CD pipelines.

### A04:2025 — Cryptographic Failures
Weak or missing cryptography for data in transit or at rest. Includes cleartext transmission, weak algorithms (MD5, SHA-1, DES), improper key management, hardcoded keys, weak randomness, and improper certificate validation.

### A05:2025 — Injection
Untrusted data interpreted as code or commands. Includes SQL, NoSQL, OS command, LDAP, XPath, ORM, expression language, and template injection. Also includes reflected, stored, and DOM-based XSS.

### A06:2025 — Insecure Design
Missing or ineffective control design — flaws that cannot be fixed by better implementation of a broken pattern. Requires threat modeling, secure design patterns, and reference architectures.

### A07:2025 — Authentication Failures
Weak identity and session mechanisms. Includes credential stuffing exposure, weak password recovery, session fixation, missing MFA, session ID exposure in URLs, and improper session invalidation.

### A08:2025 — Software or Data Integrity Failures
Assumptions of integrity that are not verified. Includes insecure deserialization, unsigned updates, CI/CD pipelines that trust unverified inputs, and auto-update mechanisms without integrity checks.

### A09:2025 — Security Logging and Alerting Failures
Insufficient logging, monitoring, or alerting to detect and respond to attacks. Includes missing audit logs for sensitive actions, unmonitored logs, logs stored only locally, and lack of alerting on anomalies.

### A10:2025 — Mishandling of Exceptional Conditions
Improper handling of errors, edge cases, and failure modes — fail-open behavior, unhandled exceptions leaking information, race conditions, and inadequate error recovery that leaves the system in an insecure state.

## Notes

- A10 is new in 2025 (replacing SSRF as a standalone category; SSRF is now folded under injection/design categories as applicable).
- Insecure Design (A06) and Cryptographic Failures (A04) retain their emphasis from 2021.
- When categorization is ambiguous, pick the primary category and note the secondary in the finding.
