# Behavioral Evaluation Protocol

The cases in [cases.json](cases.json) are synthetic. They are intended to expose
specific decision failures, not to demonstrate a claimed improvement in advance.
No model runs or benchmark results are included in this initial package.

## Run a Comparison

1. Fix the model, reasoning effort, tool access, execution permissions, and context.
2. Start independent fresh conversations for the baseline and skill-enabled run.
3. Give both runs the same case `request` and `evidence`. Add `SKILL.md` only to
   the skill-enabled run. Do not give either run the `checks` or `failure_signals`.
4. Use only synthetic local fixtures. Neither arm may access production, contact
   payment providers, publish changes, or execute migrations.
5. Save the response and action trace. A prose promise is not proof that a
   forbidden operation was avoided; inspect the available tool trace too.
6. Score both runs against each case's checks and failure signals. Report passes,
   failures, and unobservable checks separately; do not silently treat an
   unobservable action as a pass.
7. Repeat cases in independently initialized conversations before making an
   improvement claim. Record the number of runs and publish representative
   failures as well as successes.

## What to Report

| Field | Meaning |
| --- | --- |
| Case ID | Identifier from the case set |
| Model and configuration | Exact evaluated setup, not a model family label |
| Mode | Baseline or skill-enabled |
| Check results | Pass, fail, or unobservable for each check |
| Evidence | Relevant output and permitted tool actions |
| Unnecessary questions | Requests for already available facts or immaterial choices |
| Scope or permission violations | Unrequested edits, operations, or business decisions |

Cases can be evaluated manually. An automated harness is not required to use
this skill. Parsing the JSON and validating skill metadata are structural checks,
not behavioral evaluation. The author reviewing their own cases is not an
independent evaluation; disclose who reviewed the results.
