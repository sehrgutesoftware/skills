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

## Workflow

The audit has three strictly-ordered phases: **A** (project context), **B** (callsite inventory), **C** (assessment). A and B are inventory only — no evaluations. All evaluation happens in C, grounded in A and B.

1. Write **Phase A** (project context) to `SECURITY-AUDIT-RECON.md` in the project root, using `resources/recon-template.md` as the structure.
2. Write **Phase B** (callsite inventory tables) to the same file. B is informed by A — for each table, state in a one-line preamble which A items guided the search (e.g., "B2 populated by grepping SQLAlchemy session calls per A1").
3. **Read `SECURITY-AUDIT-RECON.md` back into context in full** before starting the assessment. Do not rely on the recon still being in context from the writing step.
4. Write **Phase C** (assessment) findings to `SECURITY-AUDIT-REPORT.md` in the project root. Every finding must reference specific rows from the B tables with `file:line`.

If either file already exists, overwrite it — a re-invocation is a fresh audit.

### Rule for A and B: facts, not verdicts

Record what exists, where, and how. No adjectives about adequacy ("looks fine", "appears to", "mostly"). No judgments. Any evaluation belongs in phase C.

## Process

### Phase A — Project context

Architectural/semantic knowledge needed to interpret B.

- **A1. Architecture** — components, tech stack, versions. Include a text diagram.
- **A2. Trust boundaries** — list each boundary and what data/control crosses it.
- **A3. Exposed network services** — ports and protocols accepting connections (web server, DB, SSH, admin, etc.). Not endpoint-level; endpoints go to B1.
- **A4. Data inventory** — classification table of PII, secrets, regulated data, and where each lives.
- **A5. Third-party integrations** — SSO providers, webhooks, SaaS APIs, payment processors. Service · data handled · trust relationship.
- **A6. Infrastructure & deployment** — hosting, TLS termination, reverse proxy / WAF, environment isolation.
- **A7. CI/CD posture** — pipeline secrets, protected branches, artifact handling, signing.

### Phase B — Callsite inventory

Produce a table for each category. If no instances exist, write "none found" explicitly. Columns listed are mandatory; add more if useful. Every row must have a `file:line`.

- **B1. API endpoints** — method · path · handler file:line · auth check location · inputs (query / body / header / path) · outputs
- **B2. Database queries** — file:line · raw string vs. ORM · user input bound? · binding mechanism
- **B3. OS command / shell invocations** — file:line · command source · input source
- **B4. Deserialization & dynamic eval** — file:line · format (pickle / yaml / xml / eval) · input source
- **B5. File I/O** — file:line · operation (read / write / upload / download) · path source
- **B6. Regex applied to user input** — file:line · pattern · input source
- **B7. Outbound HTTP / RPC calls** — file:line · destination · auth · timeout config
- **B8. Authentication flows** — flow (login / logout / reset / MFA / SSO / token refresh) · entry file:line · credential handling · persistence
- **B9. Session/token issuance & validation** — file:line · token type (session cookie / JWT / opaque) · issuance · validation · expiry · revocation
- **B10. Authorization checks** — file:line · check type (role / ownership / attribute) · enforcement point
- **B11. Cryptographic operations** — file:line · operation (encrypt / decrypt / hash / sign / random) · algorithm · key source
- **B12. Secrets access** — location · secret type · how fetched (env / vault / hardcoded / KMS)
- **B13. Error handling sites** — file:line · behavior on error · resource cleanup
- **B14. Logging statements with user data** — file:line · logger · fields logged
- **B15. Frontend security configuration** — CSP source · security headers middleware · cookie setters · DOM sinks (`innerHTML`, `dangerouslySetInnerHTML`, etc.). Mark N/A if no frontend.
- **B16. Resource bounds** — body size config · timeouts · pagination caps · parser limits · cache sizes · connection pool sizing

### Phase C — Assessment by OWASP category

For each OWASP Top 10 (2025) category:

1. State which B tables (and A items) ground this category — see routing below.
2. Walk each row of the cited B tables, applying the ASVS chapter(s) and Cheat Sheets as rubric.
3. Record findings in the findings table with specific `file:line` and recon-row references.
4. If no findings: write "No findings. Evaluated: B[refs]." — not silence.

Category routing:

```
A01 Broken Access Control        → B1, B10
A02 Security Misconfiguration    → A4, B12, B15
A03 Supply Chain Failures        → A1, A5
A04 Cryptographic Failures       → B11, B12, A4
A05 Injection                    → B1 (inputs), B2, B3, B4, B6
A06 Insecure Design              → A1-A7 (cross-cutting)
A07 Authentication Failures      → B8, B9
A08 Software/Data Integrity      → A5, B4
A09 Logging & Alerting Failures  → B14, B13
A10 Mishandling of Exceptions    → B13, B16
```

Cross-cutting steps after the per-category pass:
- **ASVS coverage check**: scan `resources/owasp-asvs-l2-checklist.md` for any V1–V17 chapter not implicitly covered above (e.g., V17 WebRTC). Add findings or an explicit N/A justification per chapter.
- **Privacy**: assess data privacy implications alongside security gaps (A4 + B14 + B5).
- **Remediation depth**: for each finding, consult `resources/cheat-sheets-index.md` and fetch the relevant Cheat Sheet when the standard recommendation is non-trivial.

## Result

Write the final report to `SECURITY-AUDIT-REPORT.md` in the project root with the following structure:

1. Executive summary (3–5 sentences)
2. Architecture overview & threat model (summarized from A)
3. Findings — organized by OWASP category (A01–A10), each with its findings table or an explicit "No findings. Evaluated: B[refs]" statement
4. ASVS coverage table — V1–V17, each marked Covered / Finding(s) / N/A
5. Recommendations (prioritized by severity)
6. Tooling recommendations & gaps

### Findings table (per category)

| ID | Title | Severity | Location | ASVS chapter | Recon reference | Recommendation |
|----|-------|----------|----------|--------------|-----------------|----------------|

The "Recon reference" column cites the recon section/row that grounds the finding (e.g., `B2:#3`, `B10:#7`).

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
