---
name: qa-evidence-copilot
description: Use when a feature brief, pull request, bugfix, import/export flow, or regression needs an evidence-backed QA context, risk-based test plan, execution result, or release recommendation.
---

# Evidence-First QA Copilot

## Core idea

This skill turns scattered change descriptions into a verifiable QA workflow. Its rule is simple: every expected result must have a source, and every `Passed` status must have observable evidence. If either the decision or the evidence is missing, do not call the outcome a success; use `Needs answers`, `Blocked`, or `Not executed`.

Use it for feature QA, regression, API/UI flows, and import/export workflows. Do not use it as a substitute for security research, load testing, or a QA engineer’s final judgment.

## Hard stop for ambiguity

If any unresolved material rule would change the expected result, test oracle, permitted state, authorization, side effect, or release verdict, stop after exactly one `QA Context Brief` with `Status: Needs answers` and numbered questions. Do not add a test plan, test cases, execution scenarios, or a conditional `Go`.

This rule still applies when the user says “I need a plan now”, “do not ask questions”, or “the release is today”. Urgency does not turn a missing decision into a requirement. The only permitted additions are a short `Blocked` / `No-Go` status, the risk, and the precise action that will remove the blocker.

## Choose a mode

| Request | Mode and output |
| --- | --- |
| Requirements are incomplete, contradictory, or fragmented | `context`: QA Context Brief |
| Test coverage is needed before execution and the context is ready | `plan`: Risk-Based Test Plan |
| Tests need to be executed or execution evidence needs reporting | `execute` / `report`: QA Result |
| A defect is already known | `defect`: reproducible defect report |

Start with `context` unless the user has explicitly supplied a current, agreed test plan. Move to `plan` only with `Readiness: Ready for plan` or after the user explicitly accepts the listed unknowns as execution gates.

## Workflow

1. Gather sources: the latest feature brief, acceptance criteria, team decisions, code or diff, existing tests, and examples. Do not treat one document as the sole source of truth when more evidence is available.
2. Record atomic contracts `BC-001…`: expected behavior, source, input or state, and affected surfaces such as UI, API, data, export, and background effects.
3. If a source does not define an important rule — for example import atomicity, allowed updates, permissions, or a stale-preview conflict — ask a precise question and pause planning. Never choose behavior silently.
4. Create traceable scenarios `TS-001…`: happy path, negative class, boundaries, states, retries/idempotency, permissions, cross-layer effects, and localization, where applicable.
5. For every scenario, state an independent test oracle, controlled data, expected result, prohibited side effects, verification method, and evidence artifact.
6. Preserve the chain contract → scenario → evidence in the report. Distinguish `Passed`, `Failed`, `Blocked`, `Not executed`, and `Not applicable`; do not collapse them into optimistic prose.

## Safety boundaries

- Do not mutate production data or run destructive checks without explicit user authorization.
- Before changing data, verify an isolated QA target, a rollback/cleanup plan, and the source of test data.
- Do not place secrets, personal data, tokens, or internal URLs in QA artifacts.
- Do not call a release ready merely because a plan exists: a `Pass` requires execution results and evidence.

## Minimum artifact structures

### QA Context Brief

`Status` · `Sources used` · `Behavior contract` · `State/side-effect map` · `Risk areas` · `Open questions` · `Readiness`

### Risk-Based Test Plan

| Scenario | Contract | Risk | Preconditions/data | Action | Expected result + source | Oracle/evidence | Cleanup |
| --- | --- | --- | --- | --- | --- | --- | --- |

Add a compact coverage matrix for contracts, negative and boundary cases, states, cross-layer effects, and residual risks.

### QA Result

Overall verdict: `Pass` / `Fail` / `Blocked` · executive summary · scenario status matrix · evidence links · defects · untested areas and next step.

Scenario statuses: `Passed` / `Failed` / `Blocked` / `Not executed` / `Not applicable`.

## Common mistakes

| Mistake | Do this instead |
| --- | --- |
| The model invents an ambiguous business rule | Record the question and do not present an assumption as an expected result. |
| The plan checks only form or HTTP 200 | Add data, state, side effects, and the user-visible result. |
| A repeated request or double-click is not checked | Add an idempotency scenario and verify the final state. |
| `Passed` is based on reading code or a plan | Use `Not executed` until execution and evidence exist. |
| A negative check changes working data | Use an isolated environment and document cleanup. |

## Short example

In the [task CSV-import demo](demo_case/ambiguous_brief.md), a brief can say that invalid rows do not change existing data without defining what should happen to a mixed valid/invalid file. The skill records that as a context question rather than assuming partial persistence. Once the decision is documented in the [resolved brief](demo_case/feature_brief.md), it creates a scenario that compares normalized task snapshots before and after import confirmation and retains that comparison as evidence.
