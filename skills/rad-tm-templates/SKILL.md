---
name: rad-tm-templates
description: >-
  Author, review, version, and maintain RaD-TM Threat Templates — the expert-curated lists of
  threats and pre-approved controls that ground Rapid Developer-driven Threat Modeling and keep its
  output consistent. Use this skill whenever someone (typically a security engineer or champion)
  wants to create a new threat template, derive one from a compliance standard or framework (PCI
  DSS, HIPAA, GDPR, NIST, OWASP, STRIDE) or a cloud/deployment context (AWS, Azure, GCP), review or
  tighten an existing template, add or retire threats, bump a template version, or set up template
  governance — even if they don't say "template" and just ask to "turn this standard into a
  developer threat checklist", "curate the threats our devs should check for X", or "what should be
  on our AWS threat list". This is the security-expert authoring counterpart to the per-feature
  rad-tm modeling skill; that skill consumes the templates this one produces.
---

# RaD-TM Threat Templates — authoring & maintenance

## What a Threat Template is, and why it must be constrained

A Threat Template is a **finite, expert-curated list of up to ~20 high-priority threats**, each paired with pre-approved controls and a default severity, scoped to one context (a compliance standard, a deployment environment, a coding discipline, or an organizational priority).

The template is the **guardrail** of RaD-TM. When a developer (or an AI running the `rad-tm` skill) models a feature, they select threats *from* the template rather than inventing them. This is what makes the methodology work: it lets non-experts threat model consistently, keeps results comparable across features and teams, and stops an AI from hallucinating plausible-but-irrelevant threats. The quality of every threat model therefore depends on the quality of the templates — which is why authoring is a dedicated, expert-owned activity, separate from per-feature modeling.

The discipline of a **short list (~20 max)** is a feature, not a limitation. A template is not a catalog of everything that could go wrong; it is the curated set of things a developer in this context should actually check. If you can't keep it under ~20, that's a signal to split it into two templates.

## When to use this skill

Use it for the authoring lifecycle: creating, deriving, reviewing, tightening, versioning, and retiring templates. If the task is instead "threat model *this feature*," that's the `rad-tm` skill — this one produces the inputs that one consumes.

## Template format

Each template is a single Markdown file: a short header plus one table. Full spec and field rules in `references/format-spec.md` — read it before authoring. The shape:

```markdown
# <Name> Threat Template

- **Context:** compliance | deployment | implementation | org-priority
- **Scope:** one line on what this template covers
- **Maintainer:** <team/owner>  **Version:** <vN>  **Reviewed:** <YYYY-MM-DD>

| ID | Threat | Description | Default severity | Suggested controls |
|----|--------|-------------|------------------|--------------------|
| ABC-1 | short name | what the attacker does / what goes wrong | High/Moderate/Low | specific control A; control B |
```

Strong, complete examples live in `references/examples/` (STRIDE, OWASP web, AWS). Read one before writing a new template to calibrate granularity, tone, and control specificity.

## Authoring workflow

Follow these steps when creating a new template. The aim is a list a developer can act on **without a security expert in the room** — so every entry must be concrete and every control must be actionable.

1. **Fix the context and scope.** One context per template (e.g. "PCI DSS v4 for payment-handling features," "AWS runtime hardening"). Write the one-line scope and the audience (developers vs. DevOps), since different audiences get different templates for the same feature.

2. **Source the threats.** Derive them from the authority for that context — the clauses of a standard, the categories of a framework, the failure modes of a platform, or your own incident/vulnerability data. Don't brainstorm from scratch when an authoritative source exists; map from it (see "Deriving from a standard" below).

3. **Write each threat as a concrete failure, not a category.** "Reset token is guessable or long-lived" — not "authentication weaknesses." One threat per row. Phrase it as what the attacker does or what goes wrong, so a developer can recognize whether it applies to their feature.

4. **Attach pre-approved, specific controls.** Each threat lists the controls that mitigate it, specific enough to implement directly: "enforce TLS 1.2+ on all listeners" — not "use encryption." These are the org's sanctioned answers, so a developer can pick them with confidence.

5. **Set a default severity** (High / Moderate / Low) as a sensible starting point. Developers adjust per feature during modeling; you're setting the baseline.

6. **Assign stable IDs and cap the list.** Give each threat a stable ID (`PCI-3`, `AWS-IAM1`). Never renumber existing IDs — models and backlog items reference them. Prioritize down to ~20; if more remain, split the template.

7. **Expert review.** A security expert reviews for coverage (are the real high-priority threats present?), correctness (do controls actually mitigate?), and actionability (can a developer act unaided?). This review is what makes downstream AI output trustworthy — it is not optional for production templates.

8. **Version and publish.** Stamp version + review date, then place the file where the `rad-tm` modeling skill and your developers can reach it (see README and "Publishing" below).

## Deriving a template from a standard or framework

A common and high-value task: distill a large standard (PCI DSS, HIPAA, a NIST control set) or framework (STRIDE, OWASP Top 10) into a developer-actionable template.

- **Translate clauses into developer-facing failures.** A compliance requirement like "protect stored account data" becomes a threat ("stored payment data is readable if the datastore is breached") with a concrete control ("encrypt PAN at rest with managed keys; tokenize where possible"). Developers act on the threat, not the clause number — but keep a reference to the clause in the description for auditors.
- **Filter to what's reachable at the feature level.** A standard covers org-wide governance, physical security, policy. Most of that isn't something a developer changes in a feature. Keep only the threats a developer or DevOps engineer can actually address in code or configuration.
- **Prioritize ruthlessly to ~20.** Pick the highest-impact, most-commonly-missed items for the context. Coverage of the standard is the auditor's concern; *developer leverage* is the template's concern.

## Reviewing or tightening an existing template

When asked to review or improve a template, check it against this quality bar and propose specific edits (add/reword/merge/split/retire rows, adjust severities/controls):

- **Actionable without an expert** — could a developer apply each control unaided?
- **Concrete, not categorical** — each row is a specific failure, not a theme.
- **Controls genuinely mitigate** the threat, and are specific enough to implement.
- **No gaps in the high-priority set** for the context; no low-value filler.
- **≤ ~20 threats**; if over, recommend splitting.
- **Stable IDs preserved**; new threats appended, not renumbered.
- **Right altitude** — feature-level threats a developer/DevOps can address, not org governance.

Show proposed changes as a clear before/after so the human reviewer can approve them.

## Maintenance & versioning

Templates are living artifacts. Revise when the underlying standard changes, new threats emerge, the platform evolves, or incidents/lessons-learned surface a gap. On revision:

- **Bump the version and review date.** Keep IDs stable; append new threats with new IDs; mark retired threats rather than deleting silently (so old models remain interpretable).
- **Note what changed** so consumers know why.
- **Flag downstream impact.** When a template changes, every feature model built on it should be re-run against the new version (this is the maintenance trigger in the `rad-tm` modeling skill). Call out that the affected models need re-evaluation.

## Publishing — where templates go

Authoring produces template files that the **modeling skill consumes**. Put approved templates where both your developers and the `rad-tm` skill can reach them — typically the `rad-tm` skill's `references/threat-templates/` directory and/or a shared org template repository that teams point the skill at. The README covers the operational details, roles, and cadence for humans running this process.

## Reference files

- `references/format-spec.md` — full field-by-field template spec and authoring rules. Read before creating or editing a template.
- `references/examples/` — `stride.md`, `owasp-web.md`, `aws.md`: complete, well-formed templates to calibrate against.
- `README.md` — human-facing guide: governance, roles, cadence, install/publish steps, and how the two RaD-TM skills work together.
