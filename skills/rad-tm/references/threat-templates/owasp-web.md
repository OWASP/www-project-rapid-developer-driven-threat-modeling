# OWASP Web/Application Threat Template

- **Context:** implementation (secure coding)
- **Scope:** Common web and application-layer threats, OWASP Top 10–flavored. Apply to features that expose APIs/endpoints or handle user input, sessions, or application data.
- **Maintainer:** Security team · **Version:** v1 (starter) · **Reviewed:** starter — review before production use

| ID | Threat | Description | Default severity | Suggested controls |
|----|--------|-------------|------------------|--------------------|
| OWASP-A01 | Broken access control | Missing/weak authorization lets users act outside their permissions (IDOR, forced browsing, missing function-level checks) | High | Deny-by-default; server-side object- and function-level authorization; tie resources to the authenticated principal |
| OWASP-A02 | Cryptographic failures | Sensitive data transmitted/stored without strong, correct cryptography | High | TLS 1.2+ everywhere; encrypt sensitive data at rest; vetted libraries; proper key management; no custom crypto |
| OWASP-A03 | Injection | Untrusted input interpreted as code/commands (SQL, NoSQL, OS, LDAP) | High | Parameterized queries / prepared statements; safe ORMs; input validation; escape on output; avoid shelling out |
| OWASP-A04 | Insecure design | Missing security control by design (no rate limiting, no abuse case handling) | Moderate | Threat model early (this process); security requirements; secure design patterns; abuse-case tests |
| OWASP-A05 | Security misconfiguration | Insecure defaults, open cloud storage, unnecessary features, missing headers | Moderate | Hardened baselines; least functionality; security headers; config review; disable debug in prod |
| OWASP-A06 | Vulnerable/outdated components | Known-vulnerable dependencies or unsupported versions in use | Moderate | SCA/dependency scanning; patch cadence; remove unused deps; pin and verify versions |
| OWASP-A07 | Identification & authentication failures | Weak login, credential stuffing, weak session management | High | MFA; strong password/credential policy; secure session handling; brute-force/rate protection |
| OWASP-A08 | Software & data integrity failures | Unsigned updates, insecure deserialization, untrusted CI/CD inputs | Moderate | Verify integrity/signatures of code and data; avoid unsafe deserialization; secure the pipeline |
| OWASP-A09 | Logging & monitoring failures | Security events not logged/alerted, so attacks go undetected | Moderate | Log auth and access-control events; centralized monitoring; alerting on anomalies; retention |
| OWASP-A10 | Server-side request forgery (SSRF) | Server fetches attacker-controlled URLs, reaching internal services | Moderate | Validate/allowlist outbound destinations; block internal metadata endpoints; network egress controls |
| OWASP-X1 | Cross-site scripting (XSS) | Untrusted input rendered as executable script in a victim's browser | Moderate | Context-aware output encoding; Content-Security-Policy; sanitize rich input; framework auto-escaping |
| OWASP-X2 | Cross-site request forgery (CSRF) | Victim's browser is tricked into making authenticated state-changing requests | Moderate | Anti-CSRF tokens; SameSite cookies; re-auth for sensitive actions |
| OWASP-X3 | Mass assignment | Client sets fields it shouldn't (e.g. `isAdmin`) via permissive binding | Moderate | Explicit allowlists for bindable fields; DTOs; ignore unknown fields |
| OWASP-X4 | Secrets in code/repo | API keys, passwords, tokens committed to source or shipped to clients | High | Secret management/vault; secret scanning in CI; never embed secrets client-side; rotate on exposure |
