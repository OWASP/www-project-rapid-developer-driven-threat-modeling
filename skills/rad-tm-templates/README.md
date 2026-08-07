# RaD-TM Skills — Human Guide

This guide explains how to install, use, and operate the two Rapid Developer-driven Threat Modeling (RaD-TM) skills, with a focus on **authoring and governing Threat Templates** — the curated lists that make the whole methodology work.

If you're new to the methodology itself, read the RaD-TM Methodology and Integration Guide first; this README assumes you know what a threat model, a trust boundary, and a Threat Template are.

---

## 1. The two skills and how they fit together

RaD-TM is split into two skills because it involves two different jobs done by two different people at two different times:

| Skill | Job | Who | When |
|-------|-----|-----|------|
| **`rad-tm`** | Threat model one feature end-to-end (the six stages) → produce a threat model + security backlog | Developer / DevOps | During feature design or development, per feature |
| **`rad-tm-templates`** | Create, review, version, and maintain the Threat Templates | Security engineer / champion | Ahead of time, and on an ongoing review cadence |

The relationship is a supply chain: **`rad-tm-templates` produces the templates that `rad-tm` consumes.** A developer modeling a feature selects from the templates your security team authored. The better and better-maintained the templates, the more trustworthy every threat model — which is why template authoring is a deliberate, expert-owned activity rather than something improvised per feature.

```
  Security expert                         Developer
  ───────────────                         ─────────
  rad-tm-templates  ──►  Threat Templates  ──►  rad-tm  ──►  Threat model
   (author/review)        (the guardrail)       (model)       + backlog
        ▲                                                         │
        └──────────────  feedback: gaps, new threats  ◄───────────┘
```

When a developer hits a real threat that no template covers, the modeling skill flags it as a "template-maintenance candidate." That feedback loops back to the security team, who decide whether to add it to a template — closing the loop.

---

## 2. Installing the skills

Each skill is a `.skill` file. Install whichever a given person needs (developers need `rad-tm`; security folks need both).

1. Open Claude → **Settings → Capabilities → Skills** (availability depends on your plan/workspace).
2. Upload the `.skill` file (`rad-tm.skill` or `rad-tm-templates.skill`).
3. The skill triggers automatically when you describe a relevant task — you don't invoke it by name.

> Skills are matched on their description, so you can just say what you want ("threat model this payment feature," "turn PCI DSS into a developer threat checklist") and the right skill engages.

---

## 3. Using `rad-tm` to threat model a feature

This is the everyday workflow for developers. Start a chat with the `rad-tm` skill installed and describe the feature you want to threat model. You don't need to name the skill or the methodology. Examples that trigger it:

- "Threat model this fund-transfer endpoint." (paste a design doc, user story, or the code)
- "What could go wrong, security-wise, with our new password-reset flow?"
- "Run a security design review on this feature before I build it."
- "Here's the Terraform for our upload service — what are the security risks?"
- "Update the threat model for the checkout feature; we just added a third-party payment provider."

**What to give it.** Work from whatever you already have — you don't need all of these:
- a feature description, user story, or acceptance criteria;
- a design doc, architecture note, or API/OpenAPI spec;
- source code, a pull request/diff, or infrastructure-as-code (Terraform, CloudFormation, k8s).

The more concrete the input, the better the result. If you provide code or IaC, the skill uses it to infer the real components *and* to judge whether controls are actually implemented, rather than guessing.

**What it does** — the six RaD-TM stages, in one short pass:
1. **Scope & select templates** — confirms the single feature to model and picks the applicable Threat Template(s) for the context (compliance, deployment, coding, etc.). It tells you which it chose and why.
2. **Visual model** — drafts a data-flow diagram (Mermaid) so you can confirm at a glance that it understood the feature.
3. **Trust boundaries** — identifies where trust changes (user→system, internal→external, privilege changes).
4. **Apply templates** — matches threats *from the templates* to your components. It won't invent threats; anything outside the templates is flagged separately as a maintenance candidate.
5. **Assign controls** — lists the pre-approved controls and assesses their status (implemented / partial / not / unknown), citing code or IaC where it can.
6. **Evaluate risk & create tasks** — sets a severity and status (mitigated / accepted / open) per threat, then turns every open threat and partial-control gap into ready-to-file backlog items referencing the threat ID.

**What you get.** A concise threat-model document: the diagram and trust boundaries, a threat list, a control-mapping table, a security backlog, and a short summary. Output as Markdown for quick review, or saved as a file to store alongside the feature (in the Jira/ADO ticket or the repo) as the audit trail.

**Your role: validate, don't rubber-stamp.** The skill drafts; you confirm. In particular it will *hand back to you* the calls that depend on your organization's risk appetite — whether to **accept** a threat, and the impact/likelihood weighting behind a severity. Check the diagram is right, confirm the boundaries, and make those judgment calls yourself.

**Updating an existing model.** When a feature changes (new components, data flows, integrations) or a template is revised, give the skill the prior model and the change; it re-runs the affected parts and diffs against the old version — reporting what's new, changed, or now resolved — instead of regenerating blindly.

---

## 4. Using `rad-tm-templates` to author a template

This is the core authoring workflow. Start a chat with the skill installed and describe the template you want. Examples that trigger it:

- "Create an Azure deployment threat template for our team."
- "Turn the OWASP API Top 10 into a RaD-TM threat template."
- "Review our AWS threat template and tighten the controls."
- "Add a threat for SSRF to our web template and bump the version."

**What the skill will do, and where you stay in control:**

1. **Confirm context and scope** — one context per template (compliance / deployment / implementation / org-priority), one-line scope, and the intended audience (developers vs DevOps).
2. **Source the threats** from the authority for that context (a standard's clauses, a framework's categories, a platform's failure modes, or your own incident data) rather than brainstorming blind.
3. **Draft concrete threats** — one specific failure per row, not vague categories — each with pre-approved, implementable controls and a default severity.
4. **Cap at ~20** and assign stable IDs.
5. **Hand it to you for expert review.** This step is yours: confirm coverage, that the controls genuinely mitigate, and that a developer could act on each one unaided. The skill drafts; a human approves.
6. **Version and publish** (see §6).

The skill produces a single Markdown file in the standard format. You can edit it directly afterward — it's just a table.

### What makes a good template

Keep these in mind when reviewing what the skill drafts:

- **Actionable without an expert.** Every control should be something a developer can implement directly ("enforce TLS 1.2+ on all listeners"), never "follow best practices."
- **Concrete, not categorical.** "Reset token is guessable or long-lived," not "authentication weaknesses." If a developer can't tell whether a row applies to their feature, it's too abstract.
- **Short on purpose.** ~20 threats max. The template is the curated set of what matters in this context — not a catalog of everything possible. If it won't fit, split it into two.
- **Feature-level altitude.** Include what a developer or DevOps engineer can change in code or config. Leave org-wide policy, physical, and governance controls to your compliance program — they don't belong in a developer checklist.
- **Stable IDs forever.** IDs are the link between templates, threat models, and backlog tickets. Never renumber existing threats; append new ones, and mark retired ones rather than deleting them silently.

### Deriving a template from a compliance standard

A frequent and high-value use. A full standard (PCI DSS, HIPAA, a NIST control set) is large and written for auditors. The skill helps you distill it into a developer-facing template by translating clauses into concrete failures, filtering to what's addressable at the feature level, and prioritizing to the highest-leverage ~20. The result is **not** a compliance checklist (that stays with your auditors) — it's the set of threats developers should check while building in-scope features. See `references/examples/pci-dss.md` for a worked example.

---

## 5. Reviewing and maintaining templates over time

Templates are living artifacts. Plan to revisit them.

**Trigger a review when:**
- The underlying standard or framework is revised (e.g. a new PCI DSS version).
- A new class of threat emerges or a platform changes materially.
- An incident or pen-test/audit finding reveals a gap your template should have caught.
- On a fixed cadence regardless (see §7).

**On revision:**
- Bump the **version** and **review date** in the header.
- **Append** new threats with new IDs; **mark retired** threats rather than deleting them.
- Note what changed and why.
- **Re-run affected feature models.** When a template changes, every threat model built on it should be re-evaluated against the new version. The `rad-tm` skill's maintenance mode does this per feature; your job is to signal which templates changed so teams know to re-run. Keep a record of which features used which templates so this is tractable.

---

## 6. Publishing — where templates live

Authoring produces template files; the modeling skill needs to read them. Pick one of these (most orgs do both):

- **Bundle into the `rad-tm` skill.** Place approved templates in the modeling skill's `references/threat-templates/` directory and re-package the skill, so every developer who installs it gets the current set. Best for stable, org-wide templates.
- **Shared template repository.** Keep templates in a version-controlled repo (Git) that your security team owns and developers can read. This gives you proper change history, review via pull requests, and a single source of truth. Point developers at it, or sync approved templates into the skill on a release cadence.

Either way, the principles are the same: templates are **owned** (a named maintainer), **reviewed** (a date on every production template), and **versioned** (stable IDs, explicit version bumps).

---

## 7. Suggested governance (lightweight)

You don't need heavy process, but a little structure keeps templates trustworthy:

- **Owner per template.** Named maintainer accountable for accuracy and review.
- **Review cadence.** Re-review each template at least annually, plus on the triggers in §5.
- **Approval before production.** A template is "starter" until a security expert signs off; only then is it used for real feature models. The starter templates shipped with these skills (STRIDE, OWASP, AWS, PCI DSS) are explicitly marked as needing review before production use.
- **Feedback channel.** Make it easy for developers to report template gaps surfaced during modeling (the "template-maintenance candidates" the `rad-tm` skill flags). Triage these into template updates.
- **Change log.** Record version bumps and what changed, so consumers understand why a re-run is needed.

---

## 8. Reference material in this skill

- `references/format-spec.md` — the full field-by-field template format and authoring rules.
- `references/examples/` — complete example templates to calibrate against: `stride.md`, `owasp-web.md`, `aws.md`, and `pci-dss.md` (the compliance-derivation example).

For the per-feature modeling workflow, see the `rad-tm` skill and the RaD-TM Methodology and Integration Guide.
