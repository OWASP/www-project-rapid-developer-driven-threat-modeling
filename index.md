---

layout: col-sidebar
title: OWASP Rapid Developer-driven Threat Modeling (RaD-TM)
tags: Rad-TM threat-modeling
level: 2
type: documentation
pitch: RaD-TM is a lightweight, scalable threat modeling methodology that enables developers to identify and mitigate security risks early in the design phase using reusable Threat Templates, minimizing reliance on security experts.

---

RaD-TM is a lightweight threat modeling methodology that developers can run themselves, as part of their normal workflow. You work on one feature at a time, follow a repeatable six-step process, and use reusable **Threat Templates** to find threats, map controls and prioritize risks early in design, before they turn into costly rework.

RaD-TM also ships as an **AI Skill**. Give an AI assistant a user story, design doc, API spec, or code, and it drafts the full threat model in minutes. The developer's job changes from producing the threat model to reviewing and approving it.

---

## The problem

Traditional threat modeling needs deep security expertise and tries to model whole systems at once. That doesn't fit agile development. Security teams become a bottleneck, reviews happen late, and findings arrive when changes are most expensive.

Developers need a method they can apply early and repeat on every feature, without waiting for a security expert.

## What makes RaD-TM different

**Built for developers.** As far as we know, RaD-TM is the first formal threat modeling methodology designed specifically for developers rather than security specialists.

**Threat Templates.** Templates are short, expert-reviewed lists of threats and controls for a specific context: compliance (PCI DSS, HIPAA, GDPR), deployment (AWS, Azure, GCP), or implementation (STRIDE, OWASP secure coding). Security experts write a template once, and developers reuse it on every feature. This keeps results consistent and lets threat modeling scale across large or fast-moving organizations.

**Six simple steps.**

0. Scope the feature and select Threat Templates
1. Create a visual model of components and data flows
2. Identify trust boundaries
3. Apply the Threat Templates to find relevant threats
4. Map security controls and assess whether they're implemented
5. Evaluate risk: severity and status
6. Create security tasks for the backlog

## RaD-TM with AI

The RaD-TM Skill lets an AI assistant run all six steps, so threat modeling becomes part of the AI-powered SDLC.

- **Starts from what you already have.** Feature descriptions, user stories, design docs, OpenAPI specs, source code, pull requests or infrastructure-as-code.
- **Produces a complete threat model.** This includes a Mermaid data-flow diagram, trust boundaries, a threat list, control mapping, severity ratings and backlog items ready for Jira, Azure DevOps or GitHub Issues.
- **Checks controls against evidence.** When it has code or IaC, the AI looks at what is actually implemented instead of assuming.
- **Keeps models current.** When a feature changes, the AI updates the model and reports what's new, what changed and what's resolved.

**Templates keep the AI on track.** The AI only selects threats and controls from the chosen templates. It never invents them. If it spots a real threat that no template covers, it flags it as a candidate for a new template entry instead of adding it silently. This makes the output consistent, auditable and free of invented threats.

**People still make the decisions.** The AI proposes default severities and rationales. Risk acceptance and impact or likelihood calls stay with the developer or security champion, because they depend on the organization's risk appetite.


### Methodology:
* [Markdown](https://raw.githubusercontent.com/OWASP/www-project-rapid-developer-driven-threat-modeling/refs/heads/main/rad-tm.md)
* [PDF](https://raw.githubusercontent.com/OWASP/www-project-rapid-developer-driven-threat-modeling/refs/heads/main/assets/OWASP_Rapid_Developer_Driven_Threat_Modeling_Methodology.pdf)

### Presentations:
* OWASP Global AppSec USA 2026
* [OWASP Global AppSec USA 2025](https://raw.githubusercontent.com/OWASP/www-project-rapid-developer-driven-threat-modeling/refs/heads/main/assets/OWASP_USA25_Developer-Centric_Threat_Modeling_Embedding_Security_in_Agile_Workflows.pdf)
* Boston Application Security Conference 2025
* OWASP Global AppSec EU 2025
