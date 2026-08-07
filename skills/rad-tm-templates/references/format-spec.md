# Threat Template — full format specification

A Threat Template is one Markdown file: a header block followed by a single table. This spec defines every field and the rules that keep templates consistent and consumable by the `rad-tm` modeling skill.

## Header block

```markdown
# <Name> Threat Template

- **Context:** compliance | deployment | implementation | org-priority
- **Scope:** one line describing exactly what this template covers
- **Maintainer:** <team or owner>  **Version:** <vN>  **Reviewed:** <YYYY-MM-DD>
```

| Field | Rule |
|-------|------|
| Name | Distinct and recognizable (e.g. "PCI DSS v4", "AWS Deployment", "STRIDE"). Used when developers select templates in Stage 0. |
| Context | Exactly one of: `compliance`, `deployment`, `implementation`, `org-priority`. Determines when the template is selected. |
| Scope | One line. If you need two, you probably have two templates. |
| Maintainer | The accountable owner — templates are owned, not orphaned. |
| Version | `v1`, `v2`, … Bump on any change to threats, controls, or severities. |
| Reviewed | Date of last security-expert review. Production templates must show a review date. |

## Threat table

```markdown
| ID | Threat | Description | Default severity | Suggested controls |
|----|--------|-------------|------------------|--------------------|
```

| Column | Rule |
|--------|------|
| ID | Short, stable, prefixed by template (`PCI-3`, `AWS-IAM1`, `STRIDE-T2`). **Never renumber.** Append new threats with new IDs; mark retired ones rather than reusing IDs. Models and backlog items reference these. |
| Threat | A short name for one concrete failure — not a category. |
| Description | One sentence: what the attacker does or what goes wrong. For compliance-derived templates, you may cite the source clause here for auditors. |
| Default severity | `High`, `Moderate`, or `Low`. A baseline the developer adjusts per feature. |
| Suggested controls | One or more pre-approved, specific controls separated by `;`. Specific enough to implement directly. |

## Rules that matter most

- **One threat per row, concrete not categorical.** "Reset token is guessable or long-lived" is usable; "authentication weaknesses" is not — a developer can't tell whether it applies.
- **Controls must be actionable without an expert.** "Enforce TLS 1.2+ on all listeners" — not "use encryption." The whole point is that a developer can pick the control and implement it unaided.
- **Cap at ~20 threats.** A template is a curated checklist of what matters in this context, not an exhaustive catalog. Over ~20 → split into two templates.
- **Stable IDs forever.** They are the join key between templates, threat models, and backlog tickets across versions. Renumbering breaks traceability.
- **Feature-level altitude.** Include threats a developer or DevOps engineer can address in code or configuration. Leave org-wide governance, policy, and physical controls out — they don't belong in a feature-level developer checklist.
- **Default severity is a starting point**, not a verdict. Developers re-rate per feature based on likelihood, impact, and controls already in place.

## Quality checklist (use when reviewing)

- [ ] Context is exactly one type; scope is one line.
- [ ] Every row is a concrete failure, not a theme.
- [ ] Every control is specific and implementable without an expert.
- [ ] Controls actually mitigate their threat.
- [ ] The high-priority threats for this context are all present; no low-value filler.
- [ ] ≤ ~20 threats.
- [ ] IDs are stable, prefixed, and unique; new threats appended.
- [ ] Threats are at feature level, not org governance.
- [ ] Version and review date are current.

## Common pitfalls

- **Catalog creep** — trying to cover everything. Prioritize; split if needed.
- **Vague controls** — "follow best practices" gives the developer nothing to do.
- **Category-level threats** — too abstract to match to a feature.
- **Wrong altitude** — policy/governance items a developer can't action.
- **ID churn** — renumbering on edits, silently breaking every model that referenced the old IDs.
