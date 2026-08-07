---
name: rad-tm
description: >-
  Perform Rapid Developer-driven Threat Modeling (RaD-TM) on a software feature — a lightweight,
  six-stage method that turns a feature description, user story, design doc, API spec, or
  source/infrastructure code into a complete threat model: data-flow model, trust boundaries, a
  threat list, control mapping, severity evaluation, and a ready-to-file security backlog. Use this
  skill whenever the user wants to threat model a feature or system, run a security design review,
  identify security threats and controls, find attack surface or trust boundaries, build or update a
  threat model, or generate security backlog items for something they are designing or building —
  even if they never say "RaD-TM", "STRIDE", or "threat model" by name, and even if they just
  describe a feature and ask "what could go wrong here, security-wise?". Especially relevant for
  shift-left, feature-level reviews. Threat identification is grounded in expert-curated Threat
  Templates so output stays consistent and the model never invents threats.
---

# RaD-TM — Rapid Developer-driven Threat Modeling

## What this skill is for

RaD-TM lets a developer threat model **one feature** quickly, without deep security expertise, and produces a concise, auditable threat model. It is deliberately lightweight: the whole point is to fit inside a normal development workflow (after the design/requirements phase, before code), not to model an entire application at once.

The methodology has **six stages**. Run them in order. The sections below say what each stage produces and how to do it well when an AI is driving.

The single most important idea: **the Threat Template is the guardrail.** Threats and controls are selected from a finite, expert-reviewed list — they are *not* invented freely. This is what keeps the output consistent across features and developers and stops the model from hallucinating plausible-but-irrelevant threats. If no template covers the feature's context, say so and ask which template to use rather than free-styling threats.

## Your role: validate, don't fabricate

When AI runs RaD-TM, the human's job shifts from *producing* the artifacts to *validating* them. So:

- **Generate** the diagram, trust boundaries, matched threats, controls, severities, and tasks.
- **Surface them for confirmation** — present the model as a draft the developer reviews, not a verdict.
- **Never invent threats outside the selected template(s).** If you spot something the template misses, flag it explicitly as "outside template — candidate for template maintenance," don't silently add it.
- **Defer human-judgment calls.** Accepting a threat, and the impact/likelihood weighting behind a severity, depend on the organization's risk appetite. Propose a default and rationale; leave the decision to the developer or security champion.

## Inputs

Work from whatever the developer already has — you do **not** need all of these:

- A feature description, user story, or acceptance criteria
- A design doc, architecture note, or API/OpenAPI spec
- Source code, a pull request/diff, or infrastructure-as-code (Terraform, CloudFormation, k8s manifests)

If you have code or IaC, use it: it lets you infer the real components and data flows, and it lets you judge whether controls are actually implemented (Stage 4) instead of guessing.

If the input is too thin to identify components and data flows, ask one focused question before starting (e.g. "What are the main components and where does data enter and leave?").

---

## The six-stage workflow

### Stage 0 — Scope the feature and select Threat Template(s)

Before modeling, pin down two things:

1. **The feature boundary.** One discrete feature or piece of functionality (e.g. "Transfer funds between accounts"). If the user hands you something larger, propose splitting it and confirm the slice to model first.
2. **The applicable Threat Template(s).** Templates are organized by context — compliance (PCI DSS, HIPAA, GDPR, FedRAMP), deployment (AWS, Azure, GCP, on-prem), implementation (STRIDE, secure-coding/OWASP, low-code), and organizational priorities. **Multiple templates can apply** (e.g. a PCI-scoped feature on AWS uses the PCI DSS *and* secure-code *and* AWS templates). See `references/threat-templates/` for bundled starter templates and `references/threat-templates/_format.md` for how to select and read them. (Creating or curating templates is a separate job — that's the `rad-tm-templates` skill.)

If the context is obvious from the input, select templates and state which you chose and why. If it's ambiguous, ask. Starter templates ship with this skill, but production use should rely on the organization's expert-reviewed templates — note this if you fall back to the starters.

### Stage 1 — Create a Visual Model

Produce a data-flow model of the feature: components (UI, APIs, services, datastores, external systems), the interactions between them, and the data exchanged at each interaction (mark sensitive data — credentials, tokens, PII, payment data).

Render it as a **Mermaid `flowchart`** so it is reviewable in text and renders in most tools. Keep it to the feature scope; this should be the work of a minute or two, not a full architecture diagram.

**A note on whether you even need the diagram.** The *output* of RaD-TM is the enumeration of threats and controls, and you can derive that directly from a textual or code representation — you don't need a human-drawn picture to reason about the system. So treat the diagram as an **auto-generated verification artifact**, not a mandatory gate: produce it so the developer can confirm at a glance that you understood the feature correctly, and keep it as the audit deliverable (3.1). If the developer explicitly wants to skip it, you can — but generating it is cheap and it's the fastest way for a human to catch a misunderstanding before it propagates into the threat list, so default to producing it.

See `references/deliverable-format.md` for the exact diagram + output structure.

### Stage 2 — Identify Trust Boundaries

From the model, find the points where the level of trust changes — these are the attack surfaces. Look for:

- **Trust levels**: user-to-system, internal-to-external.
- **Privilege levels**: regular-user-to-admin, service-to-service with different scopes.
- **Ownership domains**: your code vs. third-party APIs/SaaS.

Mark the security-relevant transitions where sensitive operations happen (authentication, authorization, encryption, data storage). List each boundary and the nodes it sits between. In a fully automated run this is mostly an internal step you do to scope *which* threats apply — but surface the boundaries explicitly, because they're a deliverable and an easy thing for the developer to sanity-check.

### Stage 3 — Apply Threat Template(s)

This is the core. For each selected template, walk its threats and **match the ones that apply** to this feature's components and trust boundaries. Consolidate across templates into a single threat list for the feature, de-duplicating overlaps.

For every matched threat, record: a stable threat ID (from the template), name, short description, the template it came from, the default severity (from the template), and the affected component(s)/boundary. Pull only from the template — if a real threat seems uncovered, note it separately as a template-maintenance candidate (see "validate, don't fabricate").

### Stage 4 — Assign Security Controls

For each matched threat, list the **pre-approved controls from the same template**. Then assess implementation status:

- **Implemented** — operational and effective.
- **Partially implemented** — exists but has gaps.
- **Not implemented** — missing.

If you have code or IaC, inspect it to make this assessment evidence-based (e.g. "TLS enforced in the ALB listener config" or "no rate limiting found on the login handler"), and cite what you saw. Without code, mark status as *unknown / to verify* and say so — don't assume controls are in place. Record a short rationale for each status.

### Stage 5 — Evaluate Risk

For each threat, set a **severity** (High / Moderate / Low), starting from the template default and adjusting for likelihood, impact, and the controls already in place from Stage 4. Then set a **status**:

- **Mitigated** — fully addressed by implemented controls.
- **Accepted** — acknowledged and accepted (low likelihood/impact or a business call).
- **Open** — unaddressed due to missing/partial controls.

Give a one-line rationale each. **Important:** impact/likelihood weighting and any "Accepted" decision depend on the organization's risk appetite — propose them as defaults and explicitly hand the judgment to the human. RaD-TM uses severity, not full quantitative risk ratings, on purpose — keep it light. Prioritize High-severity Open threats first.

### Stage 6 — Create Security Tasks

Turn every **Open** threat and every **Partially implemented** control gap into an actionable work item, ready to drop into Jira / Azure DevOps / GitHub Issues. Each task includes: the threat ID reference (traceable back to the model), affected components, the proposed mitigation/control, a severity-based priority, and a suggested owner (developer / DevOps / security champion) based on the control type. Don't create tasks for Mitigated or Accepted threats.

---

## Output

Assemble the deliverables and a short summary report in one threat-model document. Follow the exact structure in **`references/deliverable-format.md`** (diagram, trust-boundary list, threat list table, control-mapping table, backlog items, summary). Use `assets/threat-model-template.md` as the fill-in skeleton.

For a quick inline review, output the document in the chat as Markdown. If the user wants a deliverable to store alongside the feature (Jira/ADO/repo), save it as a Markdown file. Recommend storing it in the same ticket or feature record so it forms the audit trail.

## Maintenance mode

A threat model is a living artifact. When the user is **updating** an existing model (the feature changed — new components, data flows, integrations, or trust boundaries; or the underlying template was revised):

- Refresh the diagram and trust boundaries for the new design.
- Re-apply the relevant template(s): pick up newly introduced threats and re-evaluate existing ones against the new design.
- Reassess controls and severity for affected threats; update status and rationale.
- Refresh the backlog: open tasks for new gaps, close items no longer applicable.
- If a template version changed, re-run the feature against the new version and call out what changed.

When you have the prior model, **diff against it**: report what's new, what changed, and what's now resolved, rather than silently regenerating.

## Reference files

- `references/deliverable-format.md` — exact output structure, Mermaid diagram conventions, table schemas, and a worked mini-example. Read this before producing the final document.
- `references/threat-templates/_format.md` — how to select and read templates. Read when choosing templates in Stage 0. To create, review, or maintain templates, use the separate `rad-tm-templates` skill.
- `references/threat-templates/stride.md`, `owasp-web.md`, `aws.md` — bundled starter templates. Read the one(s) you select in Stage 0.
