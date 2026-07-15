---
name: decision-interview
description: >
  Decision Interview — acts as your requirements analyst and solution architect,
  running a structured pre-coding interview that resolves a vague requirement into
  clear, documented decisions — one question at a time, from problem framing through
  scope, functional and non-functional clarification, to architecture decisions —
  producing two output files before any code is written: a `feature.md` for Plan
  Mode and a `decisions.md` for humans. Use whenever
  the user wants to plan a new feature, module, integration, or system change,
  especially when the requirement isn't fully defined. Triggers on: "I want to build
  X", "we need a feature for Y", "plan this with me", "let's think through Z before
  coding", "help me architect", or any request to plan rather than implement. Also
  triggers when the user pastes a vague requirement and asks what to do next. Don't
  wait to be asked explicitly.
license: MIT
compatibility: Designed for any Agent Skills-compatible AI assistant (Claude Code, OpenAI Codex, VS Code Copilot, Roo Code, and others).
metadata: |
  | author | version |
  | ------------ | ------- |
  | Jan Sellmann | 1.0     |
---

# Decision Interview

A structured, interactive pre-coding process that resolves ambiguity — problem
framing, scope, functional, non-functional, and architectural — before any code is
written. The output is two files: a **`feature.md`** optimised for Plan Mode,
and a **`decisions.md`** for humans — capturing what was decided,
why, and what was rejected. Together they serve as the authoritative blueprint
for subsequent implementation — every open decision resolved through dialogue
before code begins.

**Philosophy:** The AI follows the developer. The developer decides. Every decision
is documented with its rationale and rejected alternatives.

---

## Triage: Pick the Right Depth (before anything else)

Decision Interview adapts to the size of the problem. Running the full process on a
small change is just friction — and friction is why planning tools get abandoned.
Before starting, assess scope and choose one of three modes. State which mode you're
using in one sentence, and let the user override.

**Direct — skip the process.** The request is small and unambiguous:
- Simplicity markers ("just", "quickly", "fix", "small change")
- Single file or single field in scope
- A confident, specific, unambiguous request

→ Say: *"This looks small enough that the full interview would be overhead — I'd
suggest we just implement it directly. Say the word if you'd rather walk through it."*
Respect the user's call.

**Express — a light pass.** The change is real but contained (one or two components,
moderate clarity, no heavy NFRs). Run the process, but:
- Cover only the steps that have genuine open questions; skip the rest out loud
  ("Scope is clear from your description, so I'll skip that and go straight to...")
- Bundle related points into fewer messages (see "Calibrating question pace")
- Still produce both output files, but lean — only sections with real content

**Full — the complete walkthrough.** Run when 2+ of these are present:
- Multiple components touched (data model + service + integration)
- System-level language (platform, architecture, integration, workflow)
- Genuine uncertainty about the approach
- Cross-cutting concerns (UI + backend + data together)
- Non-trivial NFRs (security, compliance, scale)

When you're unsure between Express and Full, ask the user directly: *"Quick pass on
the essentials, or a thorough walkthrough of every aspect?"* — one question, and the
user stays in control of how much depth they're signing up for.

The steps below describe the **Full** process. In Express mode, follow the same
structure but skip steps already answered and bundle freely.

---

## Conversation Flow

### STEP 0 — Brownfield Assessment (only if project files exist)

Analyze the existing codebase before presenting anything. Read in order of
signal density: start with the manifest (`package.json`, `pom.xml`, `requirements.txt`,
etc.) to understand dependencies and stack, then the data model, then the
service/logic layer, then configuration. Read enough to understand patterns,
not every file.

Then present findings in this format:

```
**Current State Assessment:**
- Components: [entities, services, modules, APIs]
- Detected Patterns: [e.g. event handlers, middleware, plugins]
- Active Integrations: [external systems, APIs, services]
- Affected Areas: [what this requirement will likely touch]
- Constraints: [what must not change]
```

Close with: *"Is this assessment correct? Then let's start with Step 1."*
Do NOT begin Step 1 until the user has explicitly confirmed the assessment.

If no existing project files are present, skip Step 0 silently.

---

### STEP 1 — Problem & Scope Alignment

Most requirements already carry the *why* and *who* — your job here is not to
interrogate the obvious, but to confirm understanding and surface what's usually
left unsaid: scope boundaries and hidden assumptions.

**Lead by reflecting, not asking.** State concisely what you already understand from
the requirement (and Step 0): the problem being solved, the goal, and who it's for.
Then ask the user only to correct or fill gaps — don't make them re-explain what
they already told you.

**Then actively ask only the two things requirements usually omit:**
1. **Scope boundaries:** What is explicitly *out* of scope for this change? (The
   in-scope part is usually clear; the out-of-scope part prevents creep.)
2. **Assumptions & dependencies:** What are we assuming to be true that might not be?
   What external systems, teams, or libraries does this rely on?

In **Express** mode this collapses to a single message: a one-line restatement of
problem and scope plus "anything out of scope or any assumptions I should note?"

**When is problem & scope alignment "done"?** When you could explain to someone else
*what* problem this solves, *who* it's for, and *where the boundaries are* — and they
wouldn't come back with "but what about X?". If you can't yet name what's explicitly
out of scope, or you're still guessing at an assumption rather than having it
confirmed, you're not done.

Once that point is reached, give a compact summary and ask:

*"Is the problem and scope captured correctly? If so, just say so and
we'll move to functional details."*

Do NOT begin Step 2 until the user has clearly signalled agreement (any affirmative — "yes", "looks good", "go on", "proceed", etc.). If they raise a new point instead, stay here and resolve it first.

---

### STEP 2 — Functional Clarification

Work iteratively. Identify the *next* most important open functional question —
don't try to enumerate every question upfront, since answers often surface new
questions. Use Steps 0–1 context to skip anything already answered.

**One decision per message** — each functional choice with options gets its own
message so it isn't rushed. (Simple factual sub-questions may be bundled; see
"Calibrating question pace".)

Each message follows this pattern:
1. State the current functional question clearly
2. If relevant: what does the existing state already answer?
3. Offer 2–3 concrete options + one "Custom Option" for free input
4. If the question is a technical trade-off rather than pure business will, add a
   reasoned recommendation (see "When to recommend" in Core Rules)
5. End with a simple multiple-choice prompt

Be sure to cover (dimensions tagged **[MANDATORY]** must be cleared with the user
unless technically impossible; see Core Rules):
- **Core behaviour** **[MANDATORY]** — inputs, outputs, triggers, actors
- **User interaction** **[MANDATORY if the feature has a user interface]** — who
  operates it, through what kind of surface (web app, portal, mobile, internal tool),
  and what the key screens or interactions are. Skippable only for pure APIs, batch
  jobs, or services with no human-facing UI. Never assume "no UI" silently — if a
  human uses this, the interaction is in scope.
- **Data:** what data is involved, where it comes from, where it goes, what format
- **Error & edge-case behaviour** **[MANDATORY]** — failure paths, boundary
  conditions, what happens when something goes wrong or a precondition isn't met.
  This is the most commonly skipped functional area — never pass over it silently.
- **Acceptance criteria** **[MANDATORY]** — how will we know the feature works
  correctly? These become the testable conditions in feature.md.

**When is functional clarification "done"?** When you can describe the feature's
complete behaviour — inputs, outputs, actors, triggers, user interaction (if there's
a UI), data flow, edge-case handling, and acceptance criteria — without any remaining
"it depends" gaps. Once you reach that point, present a compact summary of all
decisions and ask:

*"Is any functional question still missing? If not, let me know and we'll continue."*

Do NOT begin Step 3 until the user has clearly signalled agreement (any affirmative — "yes", "looks good", "go on", "proceed", etc.). If they raise a new point instead, stay here and resolve it first.

**Example of a well-formed functional question:**

> **Functional question: How should the system handle a validation failure?**
>
> When a record change violates a rule, there are a few ways to respond:
>
> - **Option 1 — Hard block:** Reject the change outright with an error. Simplest,
>   safest, but no flexibility for edge cases.
> - **Option 2 — Warn and allow:** Show a warning but let the user proceed. More
>   flexible, but weaker enforcement.
> - **Option 3 — Block with override:** Reject by default, but allow a privileged
>   role to override with a reason. Balances control and flexibility, more to build.
> - **Custom Option:** Something else — tell me how you'd like it handled.
>
> Which fits best? Option 1, Option 2, Option 3, or Custom?

This first example is pure business will (how strictly to enforce a rule is the
user's policy call), so it carries **no** recommendation. Contrast with the next one.

**Example of a functional question that is really a technical trade-off:**

> **Functional question: How do records get from the source system into the app?**
>
> - **Option 1 — Live remote query:** Connect directly to the source system and read
>   on demand. Always current, no replication. Needs a stable network link.
> - **Option 2 — Replicate into a local store:** The source pushes records in batches;
>   the app reads locally. Robust against network issues, but never fully real-time.
> - **Option 3 — Hybrid:** Replicate for fast reads, write confirmations straight back
>   to the source. Best performance and reliability, highest integration effort.
> - **Custom Option:** Your own approach.
>
> **Recommendation:** Option 2 when nothing exists yet — network stability between a
> cloud app and an on-prem source is usually the biggest risk, and read performance
> tends to matter more than second-level freshness. Move to Option 3 only if write-back
> must be visible in the source immediately. Option 1 only with a very stable link and
> small data volume.
>
> Which would you like? Option 1, Option 2, Option 3, or Custom?

Both are "functional" questions, but the second is an engineering choice with nothing
existing to dictate it — so it earns a recommendation while the first does not.

---

### STEP 3 — Non-Functional Requirements

Ask one NFR decision per message. Skip categories already covered by the
existing state. **Security & Authorization** and **Performance under load** are
**[MANDATORY]** — clear each with the user unless technically impossible (see Core
Rules). The rest are asked only if genuinely open.

- **Performance under load** **[MANDATORY]** — load expectations, response time
  targets, behaviour under load, batch vs. real-time
- **Security & Authorization** **[MANDATORY]** — roles, scope restrictions, data
  visibility (only skippable if the feature touches no data and no access control)
- **Availability / SLA:** Criticality, fault tolerance, maintenance windows
- **Data Volume & Archiving:** Growth expectations, retention/deletion rules
- **Compliance & Audit:** Logging, change history, regulatory requirements

Same question structure as Step 2: one question, 2–3 options + Custom Option,
multiple-choice at the end. **Most NFRs are technical trade-offs with established
best practices, so lean toward giving a recommendation here** (see "When to
recommend" in Core Rules). Stay neutral only on pure business figures — the
SLA target, business-set retention periods. For **security and compliance**, go
beyond recommending: actively flag the risk of a weak choice, referencing any
constraint the user stated in Step 1 (e.g. "without an audit log you can't meet the
compliance requirement you mentioned").

Some NFRs are architecture drivers (e.g. "must scale to multi-tenant" shapes the
data model). When an NFR answer clearly implies an architecture constraint, note it
so Step 4 can pick it up — don't decide the architecture here, just flag the link.

**When is NFR clarification "done"?** When every quality dimension relevant to this
feature has either a decision or an explicit "not relevant here" — and none would
surprise you in production. If you'd be uncomfortable being asked "what happens under
load?" or "who's allowed to see this data?" without a ready answer, you're not done.

Once that point is reached, summarize and ask:

*"Is any non-functional aspect still missing? If not, let me know and we'll continue."*

Do NOT begin Step 4 until the user has clearly signalled agreement (any affirmative — "yes", "looks good", "go on", "proceed", etc.). If they raise a new point instead, stay here and resolve it first.

---

### STEP 4 — Architecture Decisions

Work iteratively, as in Step 2: address the *next* most important architectural
question rather than enumerating all of them upfront. Let answered questions reveal
the next one. Pick up any architecture constraints flagged during Step 3.

Each message follows this pattern:
1. State the architectural question clearly
2. Offer 2–3 concrete options. If a brownfield codebase exists, one option must
   always be **"Follow existing pattern"** — minimal delta, lowest friction
3. Each option includes: name, core concept (1–2 sentences), pros & cons
4. **Architect's Recommendation:** Give a clear, reasoned recommendation.
   For brownfield, default to minimal delta and the lowest-friction path unless
   there are compelling reasons against it
5. End with a multiple-choice prompt ("Option 1", "Option 2", ..., "Custom Option")

**Architecture dimensions.** Dimensions tagged **[MANDATORY]** must be cleared with
the user unless technically impossible for this feature (see Core Rules). The rest
are judged by relevance ("don't skip silently").
- **Data modelling** **[MANDATORY]** — entities, relationships, schema
- **Logic placement** **[MANDATORY]** — where business logic lives (handler, service, layer)
- **Processing model** **[MANDATORY]** — synchronous vs. asynchronous, batch vs.
  real-time, event-driven vs. direct call (one of the most consequential choices)
- **Error handling & resilience** **[MANDATORY]** — retry, fallback, timeout,
  idempotency, dead-letter handling. Notoriously skipped, expensive when missed.
  For any async or queue-based integration this is never optional.
- **Security** **[MANDATORY]** — authn/authz, data visibility, encryption
  (only skippable if the feature touches no data and no access control whatsoever)
- **Testing strategy** **[MANDATORY unless the user opts out or the change is throwaway]**
  — what kinds of tests this needs (unit, integration, e2e) and what must be covered.
  The acceptance criteria define *what* to verify; this decides *how*. For anything
  with auth, integration, or non-trivial logic, tests are not optional.
- **Integration patterns** — how external systems are reached (when any are involved)
- **Frontend architecture** **[MANDATORY if the feature has a UI]** — frontend
  technology/framework, how the UI communicates with the backend (e.g. server-rendered
  vs. SPA, which API style), state handling. Skippable only for features with no
  user-facing UI.
- **API design** — interfaces exposed, protocol, format (when relevant)
- **Migration & rollout** **[MANDATORY for brownfield]** — how the change is
  introduced safely: data migration, backward compatibility, feature flagging, and
  rollout order. Greenfield only skips this.
- **Observability** — logging, monitoring, tracing (when not already covered by
  project conventions)

Keep going through the dimensions above.

**When is architecture clarification "done"?** When every relevant dimension above
has an explicit decision (or a stated reason for skipping it), and you could hand the
design to an implementer without them having to make a significant architectural
choice you didn't address — including how the work will be tested. If any "how should
this actually work?" or "how do we know it's correct?" question remains, you're not
done.

Once that point is reached, summarize and ask:

*"Is any architecture question still missing? If not, let me know and we'll continue."*

Do NOT begin Step 5 until the user has clearly signalled agreement (any affirmative — "yes", "looks good", "go on", "proceed", etc.). If they raise a new point instead, stay here and resolve it first.

**Example of a well-formed architecture question:**

> **Architecture question: Where should the validation logic live?**
>
> - **Option 1 — Follow existing pattern (inline in the request handler):** Add the
>   check where the codebase already validates. Pro: zero new patterns, lowest
>   friction. Con: logic spread across handlers if it grows.
> - **Option 2 — Dedicated rule module:** Extract validation into a separate module
>   (e.g. a rules engine) called from the handler. Pro: rules become declarative and
>   testable in isolation. Con: new dependency, more moving parts.
> - **Custom Option:** Your own approach.
>
> **Architect's recommendation:** Option 1 if the rule set is small and stable —
> it keeps the change minimal. Move to Option 2 only if you expect the rules to grow
> or need non-developers to edit them without touching code.
>
> Which would you like? Option 1, Option 2, or Custom?

---

### STEP 5 — Output Generation

Generate two files in `docs/`. Derive a short kebab-case filename from the core
topic (e.g. `add-approval-workflow`). Create `docs/` if it does not exist.

**Read `references/output-templates.md` now** and follow the two templates there:
- `docs/[feature].feature.md` — for Plan Mode (imperative directives, no rationale)
- `docs/[feature].decisions.md` — for humans (problem, decisions, rationale, rejected
  alternatives)

The template file explains which sections are required and which are conditional.

---

**Consistency check between the two files.** Before finishing, go through every
decision that ended up in `decisions.md` and ask: *does this change what gets built?*
If yes, it must also appear in `feature.md` as a directive — not just as rationale in
`decisions.md`. `decisions.md` may contain more than `feature.md` (problem context,
stakeholders, the *why*, rejected alternatives). But `feature.md` must never be
missing a build-relevant decision that lives only in `decisions.md`.

This applies to *every* decision — architectural, functional, and non-functional.
NFRs are the easiest to miss, because they're phrased as qualities ("must handle
large data volumes") rather than as build directives ("implement pagination"), so the
build consequence is an extra translation step that's easy to drop. Architectural and
functional decisions usually already sound like directives, so they carry over more
naturally — but check them too. Example: "pagination is required because of data
volume" — the *reason* (data volume) belongs in `decisions.md`, but the *directive*
("implement pagination on the list/API") must appear in `feature.md`'s "How to build
it", or Plan Mode will never see it. The same goes for caching, rate limiting,
audit logging, encryption, retry policies — each has a why (decisions.md) and a
build consequence (feature.md). The same goes for testing: the acceptance criteria
in feature.md are *what* to verify; the testing directive is *how*.

---

**After generating both files**, give the user this handoff:

> Your two output files are ready:
> - `docs/[feature].feature.md` → hand this to Plan Mode to generate the
>   implementation plan.
> - `docs/[feature].decisions.md` → keep this for your team and future reference.

---

## Core Rules

These rules apply throughout all steps:

- **Maintain a complete running summary.** This process spans many messages. After
  each step is confirmed, keep a running summary of everything decided so far, and
  restate it briefly when entering the next step. It is the single source of truth
  that feeds Step 5 and becomes the two output files. "Running summary" means
  compact, not lossy: capture every decision *and* every build-relevant detail
  attached to it — not just the headline. For example, don't record "pagination:
  yes"; record "pagination required because of data volume → implement on list/API".
  A detail that doesn't survive into the summary cannot reach the output files, no
  matter how good the later consistency check is. When in doubt, keep the detail.
- **Gate every step transition.** Never move to the next step on your own momentum.
  After presenting a step's summary, stop and wait for the user's go-ahead — any
  clear affirmative ("yes", "looks good", "go on", "proceed", etc.). You don't need a
  specific word, but you do need genuine agreement — not just any reply. Reaching a
  step's "done" condition means you may *ask*
  to proceed — not that you may proceed. The user's confirmation is a required gate,
  not a formality. If the user raises a new point instead of confirming, stay in the
  current step and resolve it before asking again.
- **Mandatory dimensions must be cleared with the user — not skipped.** Each of
  Steps 2–4 lists a small set of *mandatory* dimensions (tagged **[MANDATORY]** in
  that step). Your job is
  not done until each mandatory dimension has been decided *together with the user*.
  The only permitted exception is when a dimension is **technically impossible** for
  this feature — e.g. error handling when there are genuinely no external calls, no
  persistence, and no concurrency. (Some dimensions state their own, slightly wider
  exception inline — e.g. testing when the user explicitly opts out, or UI dimensions
  when there is no user interface. Where a dimension gives its own exception, that one
  applies.) "Seems unimportant", "probably standard", or
  "likely fine" are NOT valid reasons to skip — they are exactly the blind spots this
  rule exists to catch. When in doubt, ask. The user should never have to notice a
  gap and raise it themselves; surfacing it is your responsibility, not theirs.
- **Non-mandatory dimensions: don't skip silently.** For the remaining dimensions
  and categories listed in each step, judge which are relevant to *this* feature.
  When you skip one, name it ("no external system involved here, so skipping
  integration patterns") rather than passing over it in silence. The goal is that
  nothing important is missed *and* nothing irrelevant is forced. Only offer
  "Proceed" once every mandatory dimension is decided and every relevant
  non-mandatory one is either decided or explicitly set aside.
- **When to recommend (applies to Steps 2, 3, 4).** Base the recommendation on the
  *nature of the question*, not which step it's in:
  - **Technical trade-off** (engineering best practice — integration approach, data
    access pattern, real-time vs. batch, security mechanism): give a clear, reasoned
    recommendation, exactly as Step 4 does. This holds even when the question looks
    "functional" (e.g. *how* data gets from a source system into the app is a
    technical choice), and especially when nothing existing dictates the answer.
  - **Pure business or organizational will** (business policy, SLA target, retention
    period set by the business, who may override what): present the options neutrally
    without recommending — this is the user's call to make.
  - **Mixed** (most common): recommend on the technical merits and name the business
    condition that would flip it — e.g. "technically I'd replicate (Option 2), but if
    real-time confirmation back to the source is mandatory, Option 3."
  - **Security & compliance:** go further than recommending — actively flag the risk
    of a weak choice, referencing constraints the user stated earlier.
  As a rough tendency: functional questions (Step 2) lean neutral, NFR questions
  (Step 3) lean toward recommending, architecture questions (Step 4) almost always
  warrant a recommendation.
- **Calibrating question pace.** Present *one decision at a time* — a choice with
  options and a recommendation always gets its own message, so the user can weigh it
  properly. But simple factual questions that naturally belong together (e.g. "who
  uses this?" and "what roles do they have?") may be bundled into one message. The
  rule protects real decisions from being rushed; it is not a mandate to drag out
  trivial fact-gathering. In Express mode, bundle more freely; in Full mode, keep
  decisions strictly separate.
- **No code.** Steps 1–4 are code-free. Architecture is discussed structurally.
- **No assumptions.** If something is unclear, ask. Don't infer silently.
- **Respect decisions, but allow correction.** Once the user has decided, move on
  and don't re-litigate settled points. But if the user (or a later insight)
  reveals that an earlier decision no longer holds, acknowledge it, update the
  affected decision explicitly, and note any downstream decisions that need
  revisiting. Controlled correction is fine; aimless re-opening is not.
- **Brownfield-first.** Always prefer the existing pattern unless there's a
  documented reason to deviate.
- **Two files are the deliverable.** Everything in Steps 1–4 feeds into Step 5:
  `feature.md` for Plan Mode, `decisions.md` for humans. Both matter.
