# Security Audit Resources

Bundled OWASP references used by the Security Audit skill. These are curated snapshots — the agent should consult them first and only fetch upstream when deeper detail on a specific requirement is needed.

## Snapshot metadata

- **Snapshot date**: 2026-09-30
- **OWASP Top 10 version**: 2025 (source: https://owasp.org/Top10/2025/)
- **OWASP ASVS version**: 5.0.0 (source: https://github.com/OWASP/ASVS/tree/v5.0.0)
- **Cheat Sheet Series**: index-only, latest at time of snapshot (source: https://cheatsheetseries.owasp.org/)

## Files

- `owasp-top-10-2025.md` — the ten risk categories used to classify findings.
- `owasp-asvs-l2-checklist.md` — chapter-level ASVS v5.0.0 Level 2 checklist for verification depth.
- `cheat-sheets-index.md` — topic-to-URL index. Fetch individual sheets on demand when a finding needs deeper remediation guidance.

## Notes for the agent

- If the target codebase references a newer OWASP revision than the snapshot date above, note the drift in the report and fetch the newer version before finalizing categorizations.
- These snapshots are not a substitute for the full standards — treat them as a categorization and coverage aid.
