---
name: Security Audit
description: Perform a static security audit of the web application at hand
slash: true
---

## Goal

Produce a comprehensive static-analysis security report for the codebase at hand. Findings are categorized against the **OWASP Top 10 (2025)** and verified for coverage against **OWASP ASVS v5.0.0 Level 2**. Bundled references live in `./resources/`.

## Threat Model

Refine from what you observe. Default assumptions:

- **Actors**: unauthenticated external attacker; authenticated user attempting privilege escalation or accessing others' data; malicious insider with legitimate credentials; compromised dependency or build toolchain.
- **Assets**: user data and PII; credentials, tokens, and secrets; business-critical operations and their integrity; service availability.
- **Out of scope**: physical security, social engineering, dynamic/runtime testing (this skill is static-only), penetration testing of live infrastructure.

If the target application handles regulated data, financial transactions, or safety-critical operations, note that ASVS Level 3 rigor may be warranted and flag this in the report.

## Process

### A. Inventory
1. Architecture: components, tech stack, versions
2. Trust boundaries
3. External interfaces and attack surface (services, ports, protocols)
4. Exposed API endpoints (and their auth requirements)
5. Data inventory: PII, secrets, regulated data, and where they flow
6. Third-party integrations (SSO providers, webhooks, SaaS APIs)
7. Infrastructure and deployment: hosting, TLS termination, reverse proxy / WAF, environment isolation
8. CI/CD posture: pipeline secrets, protected branches, artifact handling

### B. Security controls
9. Authentication mechanism(s)
10. Authorization model (RBAC/ABAC, enforcement points)
11. Session management: cookie flags, JWT handling, expiry, rotation
12. Secrets management: env vars, vault, hardcoded values, `.env` in repo
13. Input validation and output encoding (framework defaults vs. custom)
14. Persistence: ORM/raw SQL, parameterization, connection strings
15. File upload/download handling
16. Resource-exhaustion and abuse protections:
    - Rate limiting on auth and expensive endpoints
    - Request body size limits (global default + per-endpoint overrides)
    - Server, database, and outbound HTTP client timeouts
    - Pagination caps on list endpoints (max page size + hard total cap)
    - Parser depth/size limits (JSON, XML, YAML) and XXE protections
    - Decompression ratio limits (zip/gzip bombs)
    - ReDoS-prone regexes applied to untrusted input
    - Bounded caches, queues, and session stores (eviction / TTL)
    - Connection pool sizing and cleanup on error paths
17. Logging and monitoring: PII in logs, audit trail for sensitive actions
18. Error handling and information disclosure, including fail-secure resource cleanup on error paths (A10:2025)
19. Cryptographic algorithms and key management

### C. Frontend (if applicable)
20. CSP, CORS, CSRF protections
21. Security headers: HSTS, X-Frame-Options / `frame-ancestors`, X-Content-Type-Options, Referrer-Policy, Permissions-Policy
22. Cookie flags: `Secure`, `HttpOnly`, `SameSite`
23. Subresource Integrity (SRI) for third-party scripts
24. Client-side token storage (localStorage vs. httpOnly cookie)
25. XSS mitigations (framework escaping, `dangerouslySetInnerHTML` / equivalents)

### D. Assessment
26. Map observations from sections A–C against OWASP Top 10 categories. See `resources/owasp-top-10-2025.md`.
27. Verify coverage against ASVS v5.0.0 Level 2 chapters (V1–V17). See `resources/owasp-asvs-l2-checklist.md`. Note any chapter that is not applicable and briefly justify.
28. Assess data privacy implications alongside security gaps.
29. For findings that need deeper remediation guidance, consult `resources/cheat-sheets-index.md` and fetch the relevant Cheat Sheet.

## Result

### Findings table

| ID | Title | Severity | Location | OWASP category | ASVS chapter | Recommendation |
|----|-------|----------|----------|----------------|--------------|----------------|

### Severity scale
- **Critical**: remote unauthenticated compromise, mass data exposure, RCE, authentication bypass affecting all users.
- **High**: authorization bypass, sensitive data exposure, stored XSS, injection with meaningful blast radius, hardcoded production secrets.
- **Medium**: reflected XSS, CSRF on non-critical actions, weak crypto configurations, missing rate limiting on sensitive endpoints.
- **Low**: missing hardening headers, verbose errors, weak defaults with low exploitability.
- **Info**: best-practice deviations with no direct exploit path.

### Tooling
- Recommend (or run, if available) SAST tools / linters appropriate to the stack: semgrep, bandit (Python), gosec (Go), eslint security plugins (JS/TS), brakeman (Rails), etc.
- Recommend (or run, if available) dependency checkers: `npm audit`, `pip-audit`, `cargo audit`, `govulncheck`, `bundler-audit`.
- Recommend (or run, if available) secret scanners: gitleaks, trufflehog.
- Note any gaps where no tooling is currently configured in CI.
