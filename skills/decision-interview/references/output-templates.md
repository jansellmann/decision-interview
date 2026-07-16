# Output Templates

The two files Step 5 generates. Copy the structure below.

Sections marked **[REQUIRED]** always appear. Sections marked **[IF RELEVANT]** are
included only when the interview produced meaningful content for them — omit them
entirely rather than leaving them empty or generic.

---

## File 1: `docs/[feature].feature.md`

For Plan Mode — machine-optimised, imperative, no history, no rationale.
Plan Mode reads this directly and should not need to ask follow-up questions.

```markdown
# [Feature Name]
Date: [YYYY-MM-DD]

## What to build [REQUIRED]
[Imperative description of what the system shall do — behaviour, actors,
triggers, edge cases and error handling. No history, no rationale.]

## How to build it [REQUIRED]
[Architecture and build directives as facts, not explanations.
"Use X. Do not use Y. Pattern: Z."
Include every directive that affects how the thing is built — including build
consequences of non-functional decisions (e.g. "implement pagination", "add caching",
"enable audit logging"). The what, not the why. The why lives in decisions.md.]

## Out of scope [REQUIRED]
[What is explicitly not being built — prevents scope creep in Plan Mode.
Even one or two lines here prevent the most common Plan Mode mistakes.]

## Acceptance criteria [REQUIRED]
- [ ] [Testable condition 1]
- [ ] [Testable condition 2]
- [ ] [Testable condition 3]

## Testing [REQUIRED unless trivial]
[Which test types (unit, integration, e2e) and what to cover for the new behaviour.
Plan Mode must create tests for the new behaviour and check which existing tests are
affected and update them. Treat test creation and adjustment as part of the
implementation, not an afterthought.]

## Affected components [IF RELEVANT — brownfield only]
[Files, modules, services, entities that will be touched.
Dependencies first, dependents after.
Omit for greenfield — Plan Mode will create the structure from scratch.]

## In scope [IF RELEVANT — only when genuinely ambiguous]
[Only include when "What to build" alone doesn't make the boundaries clear.
Skip if "What to build" already implies the scope unambiguously.]
```

---

## File 2: `docs/[feature].decisions.md`

For humans — your future self, your team, audits, retrospectives.
Answers *why*, not *what*. Links back to the feature file.

```markdown
# [Feature Name] — Decisions
Date: [YYYY-MM-DD]
Feature: docs/[feature].feature.md

## Problem & Goal [REQUIRED]
[What the problem is. What success looks like.
What happens if nothing changes.]

## Decisions [REQUIRED]

### [Decision title — e.g. "Data Access Pattern"]
**Decision:** [What was decided — one sentence]
**Rationale:** [Why — the reasoning]
**Rejected alternatives:**
- [Option A] — [why rejected, 1–2 sentences]
- [Option B] — [why rejected, 1–2 sentences]

### [Next decision title]
...

## Assumptions & Dependencies [REQUIRED when present]
[Assumptions made that could later prove false.
External systems, teams, or libraries this relies on.
Omit only if the interview surfaced no assumptions worth recording.]

## NFR Constraints [IF RELEVANT]
[Include only when NFRs meaningfully shaped the architecture —
security, compliance, scale, availability decisions with real consequences.
Skip if NFRs were standard and didn't drive architectural choices.]

## Scope boundaries [IF RELEVANT]
[Include when scope was actively negotiated or is non-obvious.
Skip if scope was clear from the requirement and Step 1 confirmed it quickly.]

## Stakeholders & Actors [IF RELEVANT]
[Include for team projects, compliance contexts, or when actor clarity
was a real open question. Skip for solo developers or obvious cases.]

## Acceptance criteria [IF RELEVANT]
[Include only as a reference link or brief summary — the full list lives
in feature.md. Skip if feature.md already carries it clearly.]
```
