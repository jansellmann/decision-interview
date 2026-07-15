# Decision Interview

**A skill that interviews you before you build.**

Structured, one question at a time — problem, scope, functional and non-functional
decisions, architecture — until every easily-forgotten thing is covered, not just the
obvious ones. Works on a new feature or against an existing codebase — it reads what's
already there before asking anything. Then it hands you two files: one for your coding
agent's plan mode, one for your team.

[![Install](https://img.shields.io/badge/install-npx%20skills%20add-blue?style=flat)](https://skills.sh)
[![Works with](https://img.shields.io/badge/works%20with-Claude%20Code%20%C2%B7%20Cursor%20%C2%B7%20Codex%20%C2%B7%20Windsurf-informational?style=flat)](#install)
[![License](https://img.shields.io/github/license/jansellmann/decision-interview?style=flat)](LICENSE)

[Why this exists](#why-this-exists) ·
[How it works](#how-it-works) ·
[The output](#the-output) ·
[Install](#install) ·
[Using it with plan mode](#using-it-with-plan-mode)

---

## Where it sits on the spectrum

```
Fast, unplanned                                                     Heavy, thorough
Vibe coding  ◄────────────  Decision Interview  ────────────►  Spec-driven development
```

| Vibe coding | Decision Interview | Spec-driven development |
|---|---|---|
| The agent makes a dozen decisions for you — data access, error handling, where the logic lives — silently. | The agent asks, one decision at a time. You choose, or take its recommendation. | You write (or generate) a full spec before touching anything. Right call for some projects, real overhead for most. |
| You find out which ones you disagree with three sprints later. | Every decision is on record, with what was rejected and why. | Multiple files, a fixed process, template-driven — heavy even for a single feature. |
| Plan mode gets a vague prompt and fills the gaps with assumptions. | Plan mode gets a `feature.md` with the gaps already closed. | Plan mode gets a spec, but not necessarily the reasoning behind it. |
| "Why does the system look like this?" has no answer six months on. | `decisions.md` has the answer. | The spec says *what* was built, rarely *why*, and rarely what was rejected. |

---

## Why this exists

Vibe coding and full spec-driven development both have a place — but a lot of real work
falls in between them, and most tooling doesn't live there. Decision Interview is a
guided conversation, not a template to fill in and not a framework to adopt. The agent
asks; you decide; the reasoning gets written down.

This isn't a brand-new idea — interviewing before building is a pattern several tools
and skills already use, and it's worth trying those too. What this skill focuses on is
a specific combination: a single standalone skill that walks you all the way to
architecture decisions, with recommendations and recorded alternatives, and depth that
adapts to the size of the task.

## How it works

**1. It picks a depth first.** A typo fix shouldn't trigger a full interview.

| Mode | When | What happens |
|---|---|---|
| **Direct** | Trivial, unambiguous change | Tells you the interview is overkill, offers to just build it |
| **Express** | Real but contained (1–2 components) | A light pass — skips what's already clear, says so out loud |
| **Full** | Multiple components, system-level scope, real uncertainty | The complete walkthrough, step by step |

**2. Then it works through the decisions, one at a time:**

| Step | Covers |
|---|---|
| Brownfield assessment *(existing codebase only)* | Reads the relevant existing code first, so it doesn't ask what it can already see |
| Problem & scope | What you're solving — and just as important, what's explicitly out of scope |
| Functional clarification | Behaviour, inputs, outputs, edge cases, acceptance criteria, the UI if there is one |
| Non-functional requirements | Performance, security, the qualities that quietly shape the architecture |
| Architecture decisions | Data model, where logic lives, sync vs. async, integration, error handling, testing |

**3. A few things it deliberately does:**

- **Recommends on engineering trade-offs, stays neutral on business calls.**
  "Replicate or query live?" gets a reasoned recommendation. "Should managers be able
  to override?" is your call — it just lays out the options.
- **Won't silently skip the expensive-to-miss dimensions.** Error handling, security,
  tests, and (for anything with a UI) the interface itself are mandatory — decided
  with you, or explicitly set aside with a reason. You never have to notice the gap
  yourself.
- **Waits for you at each step.** No running ahead on its own momentum. Any clear
  "yes, go on" moves it forward; raise a new point instead and it stays put.
- **Records the rejected alternatives.** So old debates don't reopen six months later.

## The output

Two files, two audiences.

**`docs/[feature].feature.md`** — for plan mode. Imperative, no rationale, no history:

```markdown
# Order Approval Workflow

## What to build
Orders above a configurable value threshold require manager sign-off before release...

## How to build it
Use an event-driven flow via the message queue. Do not call the ERP synchronously.
Implement pagination on the order list (large data volume)...

## Out of scope
Bulk approval. Delegation of approval authority...

## Acceptance criteria
- [ ] Orders over the threshold cannot be released without a recorded approval
- [ ] ...

## Testing
Unit tests for the threshold logic; integration test for the approval-to-release path.
Check and update existing order-release tests.
```

**`docs/[feature].decisions.md`** — for you and your team. The reasoning, the
assumptions, the alternatives that were considered and dropped:

```markdown
## Decisions

### Integration pattern
**Decision:** Event-driven via the message queue.
**Rationale:** Network stability between the cloud app and the ERP is the biggest
risk; read performance matters more than second-level freshness.
**Rejected alternatives:**
- Live remote query — needs a stable link we don't have; simplest, but fragile.
- Synchronous call with write-back — best consistency, highest integration effort;
  not justified unless confirmation must be visible in the ERP immediately.
```

The first one builds the feature. The second one answers "why does the system look
like this?" six months later, when nobody remembers.

## Install

Install with [skills.sh](https://skills.sh)'s CLI, which works across Claude Code,
Cursor, Codex, Windsurf, and other agents that support the Agent Skills format:

```bash
npx skills add jansellmann/decision-interview
```

## Using it with plan mode

The whole point is a clean handoff. Once the interview is done, point your coding
agent's plan mode at `feature.md`. Because the decisions are already made, the plan
reflects your intent instead of the agent's assumptions.

---

## Feedback

Built by [Jan Sellmann](https://github.com/jansellmann). Issues and pull requests
welcome — especially real-world runs where the interview missed something it should
have caught. That kind of feedback is exactly what shaped the skill so far.
