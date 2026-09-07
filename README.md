# Business Code Guardrails

**Before AI changes business behavior, make it show where the rule comes from.**

[简体中文](README.zh-CN.md) | [Skill instructions](SKILL.md) | [Synthetic examples](examples/scenarios.md)

A lightweight, instruction-only skill for changing and reviewing existing
business systems. Its focus is a specific failure mode: code that looks reasonable
and passes tests, but implements a business rule nobody actually confirmed.

## When to Use It

- Changing cancellation, refund, settlement, inventory, or eligibility rules.
- Interpreting third-party statuses, identifiers, or callback behavior.
- Reviewing changes that affect persisted data, permissions, or older clients.
- Maintaining a system where the requirement is clear but its existing business
  path and side effects need investigation.

Skip it for cosmetic edits, generic programming questions, or workflows that
already provide equivalent rule tracing. It is not a requirements framework,
static analyzer, security boundary, or replacement for tests and human review.

## What It Asks the Agent to Do

1. Separate current implementation, intended behavior, and external contracts.
2. Trace only the code paths relevant to the requested change.
3. Attach evidence to material business conditions.
4. Ask about consequential unknowns without blocking ordinary technical choices.
5. Check relevant historical effects and report actual verification.

There are no runtime dependencies, hooks, telemetry, or automatic commands in
this repository. The coding agent still uses its own tools and permissions.

## A Small Example

**Request:** "Allow cancellation before collection."

**Insufficient reasoning:** "The status is below SHIPPED, so cancellation is safe."

**Expected reasoning:** Locate the collection signal and all cancellation entry
points. Check whether stored status numbers are ordered at all. Preserve unrelated
product behavior. If the carrier's collection event is unknown, ask for that
mapping before implementing the eligibility check.

This is a synthetic illustration, not a measured before/after result. More
examples cover settlement and provider status mapping in [scenarios](examples/scenarios.md).

## Install in Codex

For a new user-level installation:

```bash
mkdir -p "$HOME/.agents/skills"
git clone https://github.com/289989142/business-code-guardrails.git \
  "$HOME/.agents/skills/business-code-guardrails"
```

Review the files before using them. If that directory already exists, inspect
your installation instead of overwriting it. Avoid keeping multiple installed
copies with the same skill name, including any existing legacy installation.

Then invoke it explicitly:

```text
Use $business-code-guardrails to review the current diff.
Trace the business rules and historical behavior. Do not edit code or run builds.
```

For team use, the skill folder can live under `.agents/skills/` in the target
repository. Match the installation location to your host version. See the
[official Codex skill documentation](https://learn.chatgpt.com/docs/build-skills).
Other agents that accept `SKILL.md` may use the instructions, but cross-host
discovery and behavior have not been validated here.

## Which Skill Should I Choose?

| Your immediate need | Consider |
| --- | --- |
| Clarify an ambiguous requirement and define acceptance criteria | [ECC: intent-driven-development](https://github.com/affaan-m/ecc/blob/main/skills/intent-driven-development/SKILL.md) |
| Trace business rules while changing or reviewing existing code | This skill |
| Investigate the root cause of a reported failure | [Superpowers: systematic-debugging](https://github.com/obra/superpowers/blob/main/skills/systematic-debugging/SKILL.md) |
| Adopt a broader engineering workflow | [Addy Osmani: agent-skills](https://github.com/addyosmani/agent-skills) |

These are related projects, not dependencies or endorsements. Their scopes
overlap. Add this skill only if it fills a gap in your workflow; do not stack
conflicting instructions or let a skill expand execution permissions.

## Evaluation and Limits

[Evaluation cases](evals/cases.json) and a [comparison protocol](evals/README.md)
are included. They cover requirement conflicts, missing provider semantics,
persisted enums, execution limits, and unnecessary clarification.

The cases are a proposed evaluation set, **not completed behavioral tests**.
No error-reduction percentage, model ranking, or cross-agent compatibility
claim is made. Structural validation does not demonstrate better decisions.

## Contributing

Open an issue or pull request with a small synthetic reproduction, the requested
behavior, the observed agent decision, and the relevant evidence. Do not include
company source code, customer data, credentials, internal endpoints, or private
conversation transcripts. Prefer a demonstrated failure over adding another
blanket rule.

## License

[MIT](LICENSE).
