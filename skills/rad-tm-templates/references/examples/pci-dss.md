# PCI DSS Threat Template

- **Context:** compliance
- **Scope:** Threats relevant to features that store, process, or transmit payment card data (cardholder data environment). Developer-facing distillation of PCI DSS — clause references in descriptions are for auditors; developers act on the threat and controls.
- **Maintainer:** Security / Compliance · **Version:** v1 (starter) · **Reviewed:** starter — review before production use

> Example of deriving a developer template from a large standard: each row turns a PCI requirement area into a concrete failure a developer can recognize and act on, filtered to feature-level items and capped at the highest-leverage threats. It is **not** a complete PCI compliance checklist — that remains the auditor's scope.

| ID | Threat | Description | Default severity | Suggested controls |
|----|--------|-------------|------------------|--------------------|
| PCI-1 | Full PAN stored or displayed | Primary Account Number persisted or shown in full where it isn't needed (Req. 3) | High | Don't store PAN unless required; mask to first6/last4 on display; tokenize; truncate |
| PCI-2 | Cardholder data unencrypted at rest | Stored card data readable if the datastore is breached (Req. 3) | High | Strong encryption at rest with managed keys; restrict key access; tokenize to remove data from scope |
| PCI-3 | Cardholder data unencrypted in transit | Card data sent over untrusted networks without strong transport encryption (Req. 4) | High | TLS 1.2+ on all paths carrying card data; reject weak ciphers; validate certificates |
| PCI-4 | Sensitive authentication data retained | CVV/CVC, full track, or PIN stored after authorization (Req. 3) | High | Never persist sensitive authentication data post-auth; scrub from logs and memory |
| PCI-5 | Weak access control to card data | More users/services can reach cardholder data than need to (Req. 7) | High | Least-privilege, role-based access; deny by default; restrict to a documented need-to-know |
| PCI-6 | Shared or weak authentication to CDE | Shared accounts or single-factor access to systems handling card data (Req. 8) | High | Unique IDs per user/service; MFA for access to the cardholder data environment |
| PCI-7 | Insufficient logging of card-data access | Access to and changes affecting cardholder data not logged, so misuse is undetectable (Req. 10) | Moderate | Log access and admin actions on card data with actor/time/outcome; centralize; protect log integrity |
| PCI-8 | Card data in logs / non-prod / test data | PAN leaks into application logs, error messages, analytics, or test fixtures (Req. 3) | High | Scrub PAN from logs and errors; never use live PAN in test/non-prod; data-loss checks in CI |
| PCI-9 | Known vulnerabilities in CDE components | Unpatched libraries/services in the card-handling path (Req. 6) | Moderate | Dependency scanning; timely patching; remove unused components in the CDE path |
| PCI-10 | Insecure coding in payment flows | Injection, broken access control, or other app flaws in card-handling code (Req. 6) | High | Secure coding practices; parameterized queries; server-side authorization; security testing of payment flows |
| PCI-11 | Card data crosses into out-of-scope systems | Cardholder data flows to systems not designed/assessed for PCI scope, widening exposure (Req. 1) | Moderate | Segment the CDE; tokenize before data leaves scope; restrict and document flows across boundaries |
