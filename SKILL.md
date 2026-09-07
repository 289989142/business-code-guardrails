---
name: business-code-guardrails
description: Ground changes and reviews of existing business code in traceable rules. Use for money, eligibility, permissions, persisted states, callbacks, and external field semantics. Distinguish current behavior from intended requirements; identify material unknowns before changing behavior. Not needed for cosmetic edits or generic coding questions.
license: MIT
metadata:
  short-description: Trace business rules before changing code
---

# Business Code Guardrails

Use this skill to make business-sensitive changes reviewable, not to create a new
development process. Follow the user's requested mode: a review or explanation
does not authorize implementation. Keep output proportional to the change.

## Do Not Invent Business Rules

A plausible interpretation, industry convention, field name, status label, or
passing test is not sufficient evidence of intended business behavior. Establish
the relevant rule and its scope before encoding it.

This matters especially for amounts, eligibility, quotas, tenant boundaries,
persisted enums, order states, refunds, settlement, retries, callbacks, scheduled
jobs, and third-party identifier mappings.

## Separate Three Kinds of Evidence

| Question | Evidence | Limitation |
| --- | --- | --- |
| What does the system do today? | Relevant implementation, callers, consumers, schema, fixtures, and tests | Current behavior can be wrong; it does not define a new requirement. |
| What should this change do? | The latest explicit requirement and applicable product or business decisions | A local decision does not redefine an external protocol. Surface conflicting commitments. |
| What does the integration allow? | Applicable official contract, versioned API documentation, structured responses, and confirmed provider clarification | Example values and observed payloads do not establish every boundary or guarantee. |

Do not rank all sources in one universal hierarchy. Check scope, version, and
recency. A confirmed new rule can intentionally replace old behavior. When
sources disagree, name the conflict and its consequence instead of silently
choosing whichever source makes implementation easiest.

## Establish the Smallest Relevant Context

- Inspect the working-tree state, where available, and preserve unrelated work.
- Trace the affected entry point through services, persistence, and relevant
  consumers or jobs. Include other systems only where the changed contract reaches them.
- Look up facts available in authorized local code and documentation before asking
  the user. Missing database access is not permission to access production.
- Identify the material business conditions and their sources. Use a brief list;
  a rule table is useful only when several conditions interact.

For a rule table, these columns are sufficient:

| Condition | Current behavior and source | Intended change and source | Open issue |
| --- | --- | --- | --- |
| The condition being changed | File/symbol, fixture, or documented behavior | Confirmed requirement or contract | Only a decision that affects this change |

## Handle Unknowns Without Blocking Everything

Ask a focused question when an unresolved rule could change money, eligibility,
access, a state transition, data interpretation, or an external side effect.
Pause the affected mutation, not unrelated investigation or already-clear work.

Choose ordinary technical details using repository conventions when they do not
decide business behavior. Do not ask the user to choose a local variable name or
explain a fact that can be established from the repository.

Label inferences explicitly. Do not turn a material, unconfirmed business
inference into executable behavior. If a decision is unavailable, report the
specific missing rule and what work remains possible.

## Check Only Relevant Historical Effects

- **Persisted values:** Find readers and writers before changing enum values,
  defaults, field units, or identifier meaning. Do not renumber stored statuses
  merely to make their order convenient.
- **Shared paths:** Check other callers, clients, tenants, or product types that
  use the changed logic. Keep a product-specific rule within its confirmed scope.
- **Async behavior:** Where callbacks, retries, or jobs are involved, inspect
  duplicate delivery, out-of-order delivery, partial failure, and repeated side
  effects. Do not assume an idempotency guarantee or implement one without
  checking the existing mechanism and provider contract.
- **Integration semantics:** Prefer structured status fields to free-text
  messages. Unknown values are unknown, not automatically success or failure.
- **Database changes:** Verify the affected row set, units, null handling, and
  compatibility. Use repository query patterns; do not turn a narrow business
  change into a generic data-access refactor.

Do not require every check for every task. Preserve unaffected behavior; treat
intentional changes as explicit requirements, not as compatibility defects.

## Preserve Execution Boundaries

This skill grants no additional permissions. Respect the user's and repository's
limits on edits, tests, builds, network access, production reads, DDL, deployment,
restarts, commits, and publishing. A need for verification does not override a
restriction on running it.

Do not copy credentials, personal records, internal URLs, or production payloads
into public examples or reports. Use synthetic fixtures when illustrating a rule.

## Report What Can Be Checked

Summarize the changed or reviewed rule, its sources, relevant compatibility
effects, and remaining material unknowns. Reference files or symbols where useful.
For a review, put actionable findings first and distinguish a confirmed defect
from an unresolved business question.

Say what verification actually ran and what it established. Distinguish static
inspection, automated tests, and integration verification. Passing tests do not
prove that the tested business rule is intended. Do not claim deployment or
end-to-end success from a code change alone.

This is an instruction-level aid, not an enforcement engine or a guarantee of
correctness. Human review and appropriate verification still matter.

For worked illustrations, read [synthetic scenarios](examples/scenarios.md) only
when an example helps resolve how to apply these rules.
