# Threat Templates — selecting and reading them

Threat Templates are RaD-TM's guardrail: finite, expert-curated lists of up to ~20 threats with pre-approved controls and default severities, scoped to one context. In Stage 3 you select threats **from** these templates — you never invent threats from a blank page. That constraint is what keeps output consistent and hallucination-free.

> **Authoring is a separate job.** To *create, derive, review, version, or maintain* templates, use the **`rad-tm-templates`** skill, which is built for security experts and contains the full authoring spec and governance guidance. This file covers only what you need to *select and read* templates while modeling a feature.

## Selecting templates (Stage 0)

Templates are organized by context, and **more than one can apply** to a feature — apply all that fit and consolidate the results into one threat list.

| Context type | Examples | When to apply |
|--------------|----------|---------------|
| Compliance | PCI DSS, HIPAA, GDPR/Privacy, FedRAMP | Feature handles regulated data or is in audit scope |
| Deployment | AWS, Azure, GCP, on-prem | Match the feature's runtime environment |
| Implementation | STRIDE, secure-coding/OWASP, low-code/no-code | STRIDE for a general first pass; OWASP for web/app code |
| Organizational priorities | Internal vuln patterns, high-risk areas | Org has its own curated list |

Different audiences may use different templates on the same feature (developers use PCI DSS + secure-code; DevOps uses the AWS template). If the context is clear from the input, select and state which templates you chose and why; if ambiguous, ask.

## Reading a template

Each template is a Markdown file with a header (Name, Context, Scope, Maintainer/Version/Reviewed) and a table with columns: `ID | Threat | Description | Default severity | Suggested controls`. In Stage 3 match rows by whether the described failure is reachable across one of the feature's trust boundaries; carry the **ID**, **default severity**, and **controls** through into your threat list and control mapping.

## Bundled starter templates

These ship with the skill as working examples — **starting points, not a substitute for expert review.** Production use should rely on your organization's reviewed templates.

- `stride.md` — general-purpose, STRIDE categories. Good default first pass for almost any feature.
- `owasp-web.md` — web/application secure-coding threats (OWASP Top 10 flavored).
- `aws.md` — AWS deployment/runtime threats.

To add your organization's templates, drop additional `<name>.md` files (authored via the `rad-tm-templates` skill) into this directory; they become selectable in Stage 0.
