# STRIDE Threat Template

- **Context:** implementation (threat-modeling framework)
- **Scope:** General-purpose, technology-agnostic threats organized by the six STRIDE categories. Good default first pass for almost any feature.
- **Maintainer:** Security team · **Version:** v1 (starter) · **Reviewed:** starter — review before production use

> STRIDE = Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Elevation of privilege. Match the rows whose described scenario is reachable across one of the feature's trust boundaries.

| ID | Threat | Description | Default severity | Suggested controls |
|----|--------|-------------|------------------|--------------------|
| STRIDE-S1 | Identity spoofing | Attacker authenticates as another user or service by stealing/guessing credentials or reusing tokens | High | Strong authentication; MFA for sensitive actions; short-lived tokens; session binding |
| STRIDE-S2 | Spoofed source/endpoint | Caller impersonates a trusted upstream system or callback origin | High | Mutual TLS or signed requests between services; verify token audience/issuer; allowlist origins |
| STRIDE-T1 | Request/parameter tampering | Attacker modifies inputs, IDs, prices, or hidden fields to change behavior | High | Server-side validation; never trust client-supplied authorization data; integrity checks |
| STRIDE-T2 | Data-in-transit tampering | Man-in-the-middle alters data between components | Moderate | Enforce TLS 1.2+ on every hop; certificate validation; HSTS |
| STRIDE-T3 | Stored-data/config tampering | Unauthorized modification of data at rest or configuration | Moderate | Least-privilege write access; integrity monitoring; signed config; audit of changes |
| STRIDE-R1 | Insufficient logging / repudiation | Users or attackers can deny actions because security-relevant events aren't logged | Moderate | Tamper-evident audit logs of sensitive actions with actor, time, and outcome; centralized log store |
| STRIDE-I1 | Sensitive data exposure | Confidential data (PII, secrets, tokens) leaks via responses, logs, errors, or storage | High | Encrypt at rest and in transit; data minimization; scrub secrets from logs/errors; access controls |
| STRIDE-I2 | Excessive data in responses | API returns more fields/records than the caller needs, enabling enumeration/scraping | Moderate | Field-level filtering; object-level authorization; pagination limits |
| STRIDE-I3 | Verbose error / info leak | Stack traces or internal details disclosed to clients aid attackers | Low | Generic client errors; detailed errors only server-side |
| STRIDE-D1 | Resource-exhaustion DoS | Attacker overwhelms the feature with expensive or high-volume requests | Moderate | Rate limiting; quotas; timeouts; pagination; autoscaling with caps |
| STRIDE-D2 | Amplification / unbounded operation | A single request triggers disproportionate work (large queries, fan-out) | Moderate | Input size limits; query cost limits; circuit breakers |
| STRIDE-E1 | Vertical privilege escalation | A lower-privileged user performs admin/elevated actions | High | Server-side authorization checks on every privileged action; deny-by-default |
| STRIDE-E2 | Horizontal privilege escalation (IDOR) | A user accesses another user's objects by changing an identifier | High | Object-level authorization tying resources to the authenticated principal |
| STRIDE-E3 | Privilege escalation via dependency/supply chain | Compromised library or component runs with the feature's privileges | Moderate | Dependency pinning and scanning; least-privilege runtime; integrity verification |
