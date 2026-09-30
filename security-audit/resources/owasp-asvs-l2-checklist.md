# OWASP ASVS v5.0.0 — Level 2 Verification Checklist

Chapter-level checklist for verification depth. Use during the assessment step to ensure coverage across all ASVS domains. For the full requirement text of any chapter, fetch from https://github.com/OWASP/ASVS/tree/v5.0.0/5.0/en/.

Source: https://github.com/OWASP/ASVS/tree/v5.0.0 (snapshot: 2026-09-30)

Level 2 represents standard security practices expected for most production web applications. Escalate to L3 (high-assurance) only when the application handles regulated data, financial transactions, or safety-critical operations.

## Chapters

### V1 — Encoding and Sanitization
Contextual output encoding for HTML, JS, URL, CSS contexts. Injection prevention via parameterized queries, safe APIs, and library-provided escaping. Verify no manual string concatenation for SQL, OS commands, LDAP filters, or XPath queries.

### V2 — Validation and Business Logic
Input validation is applied server-side (client-side is UX only). Positive validation (allowlist) preferred over deny-list. Business logic constraints enforced (rate limits, workflow order, atomicity). Anti-automation controls for sensitive actions.

### V3 — Web Frontend Security
Security headers: CSP, HSTS, X-Frame-Options / frame-ancestors, X-Content-Type-Options, Referrer-Policy, Permissions-Policy. Cookie flags: Secure, HttpOnly, SameSite. Subresource Integrity for third-party scripts. Clickjacking, MIME-sniffing, and open-redirect protections.

### V4 — API and Web Service
REST/GraphQL: authentication on every endpoint, method-appropriate auth checks, schema validation, no mass assignment. CORS allowlist is explicit and minimal. GraphQL query depth/complexity limits. Content-type enforcement.

### V5 — File Handling
Upload: type validation by content (not extension), size limits, virus scanning where applicable, storage outside webroot, non-executable permissions. Download: authorization checks, path traversal prevention. No user-controlled filenames served directly.

### V6 — Authentication
Password storage: Argon2id / bcrypt / scrypt / PBKDF2 with appropriate work factors. MFA available and enforced for sensitive accounts. Credential recovery flows do not reveal account existence. Rate limiting on auth endpoints. No default credentials.

### V7 — Session Management
Cryptographically strong session IDs (≥64 bits entropy). Session invalidation on logout and privilege change. Idle and absolute timeouts. Session fixation prevention (rotate on login). No session IDs in URLs.

### V8 — Authorization
Enforced server-side at every access. Deny-by-default. Least privilege. Object-level authorization (IDOR prevention) checked on every reference to user-owned data. Role/permission checks not solely based on client-supplied fields.

### V9 — Self-contained Tokens (JWT)
Explicit algorithm allowlist (reject `none`). Signature verification before any claim use. Standard claims validated: `exp`, `nbf`, `iss`, `aud`. Short lifetimes for access tokens. Revocation strategy defined. Sensitive data not stored in JWT payload.

### V10 — OAuth and OIDC
Authorization Code + PKCE for public clients. State parameter enforced to prevent CSRF. Nonce validated for OIDC. Redirect URIs strictly matched (no wildcards). Refresh token rotation. Scopes minimized.

### V11 — Cryptography
Approved algorithms only (see ASVS Appendix C). No custom crypto. AES-GCM or ChaCha20-Poly1305 for symmetric; RSA-2048+ or ECDSA P-256+ for asymmetric. SHA-256+ for hashes (never MD5/SHA-1 for security). CSPRNG for all security-sensitive randomness.

### V12 — Secure Communication
TLS 1.2+ (prefer 1.3) with strong cipher suites. Valid certificates, no self-signed in production. Internal service-to-service traffic also encrypted. Certificate pinning where threat model warrants. HSTS with `includeSubDomains`.

### V13 — Configuration
Secrets not in source control, not in container images, not in logs. Secret management via vault / KMS / env vars injected at runtime. Debug modes off in production. Default accounts removed. Minimal attack surface (unused features disabled).

### V14 — Data Protection
Classification of data (PII, secrets, financial, health). Encryption at rest for sensitive data. Data minimization — only collect what's needed. Retention policies enforced. Backups encrypted. Secure deletion procedures. GDPR/privacy considerations documented.

### V15 — Secure Coding and Architecture
Trust boundaries documented. Threat modeling artifacts exist. Fail-secure defaults. Defense in depth. Separation of duties. Third-party components inventoried (SBOM). Deprecated/EOL dependencies flagged.

### V16 — Security Logging and Error Handling
Security-relevant events logged: authentication (success/failure), authorization failures, input validation failures, privilege changes, data access to sensitive records. Logs include timestamp, actor, action, source. No secrets or PII in logs. Log integrity protected. Errors return generic messages to users; details logged server-side.

### V17 — WebRTC
Only applicable if the app uses WebRTC. Signaling channel authenticated and integrity-protected. DTLS-SRTP for media. TURN server hardened. ICE candidate filtering.

## How to use

1. For each chapter, verify the listed controls exist and are correctly applied in the target codebase.
2. Note any chapter that is not applicable and briefly justify (e.g., "V17 — no WebRTC in this app").
3. Findings tie back to the chapter (e.g., a missing `SameSite` cookie flag is a V3 finding, mapped to A05:2025 or A02:2025).
4. When a control needs deeper verification, fetch the specific chapter's requirement list from the ASVS v5.0.0 repo.
