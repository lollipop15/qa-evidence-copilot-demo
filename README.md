# Evidence-First QA Copilot

> A public, clean-room example of an original AI skill for QA. All product entities, requirements, test data, and the synthetic QA result are fictional. The [validation note](validation/skill_evaluation.md) separately documents a limited, real evaluation of the skill instruction against a synthetic prompt.

## What it does

This is not “a prompt that writes test cases.” The skill turns a product change into a controlled workflow:

`requirements → verifiable contract → risk-based plan → evidence → QA verdict`

It streamlines routine QA analysis: gathering context, numbering verifiable contracts, building a risk-and-scenario matrix, prompting for an independent test oracle, and producing a reproducible report. Product decisions, priorities, and final quality approval remain with a human.

## If HR reads only one screen

| Question | Short answer |
| --- | --- |
| What problem does it solve? | Under deadline pressure, an LLM can produce a long but assumption-driven test list. That plan does not demonstrate quality. |
| What did the author create? | A strict QA workflow that first establishes what “correct” means, then links coverage to observable evidence. |
| What does the demo show? | On an ambiguous requirement, the skill stops and asks for a product decision. On an agreed requirement, it creates a traceable plan and an honest synthetic defect report. |
| What is the team impact? | Less manual QA administration and fewer blind spots; faster preparation of QA artifacts; clearer separation between what is proven and what still needs a decision. |

## A three-minute HR walkthrough

1. Open [SKILL.md](SKILL.md): its key rule is that a scenario cannot be marked `Passed` without observable evidence.
2. Show the [ambiguous brief](demo_case/ambiguous_brief.md) and the [context that prevents a guess](demo_output/blocked_context.md). This matters more than a long test list: the AI does not replace the product owner’s decision.
3. Then open the [agreed brief](demo_case/feature_brief.md), [risk-based test plan](demo_output/risk_based_test_plan.md), and [evidence-backed QA result](demo_output/qa_result.md).
4. End with: “My value is not asking a model to write test cases. It is making the model a disciplined QA assistant whose conclusions can be inspected and verified.”

### Mini glossary

- **Contract** — one specific rule that can be tested, for example: “an invalid file persists nothing.”
- **Evidence** — an observable test artifact: a data snapshot, file, operation response, or screenshot.
- **Oracle** — an independent way to determine the expected result; it must not merely repeat the response from the system under test.
- **Atomic import** — either the entire valid file is persisted, or nothing changes when an error occurs.

## 60-second pitch

> I created Evidence-First QA Copilot, an AI skill for recurring QA work. It starts with a short change description but does more than generate test cases: it turns requirements into atomic, verifiable contracts and then creates a risk-based plan with negative, boundary, stateful, and cross-layer scenarios. When execution is available, it brings snapshots, API responses, screenshots, and export results together in a single QA report.
>
> Its central rule is simple: if the expected behavior has no authoritative source, or execution has no observable evidence, the skill does not simulate confidence. It records `Needs answers`, `Blocked`, or `Not executed`. This reduces routine work, makes coverage transparent, and prevents a test plan from being mistaken for a testing result. AI helps the QA engineer think systematically; it does not replace professional judgment.

## How it differs from a typical LLM prompt

| Typical prompt | Evidence-First QA Copilot |
| --- | --- |
| One-off test list | Reusable workflow with inputs, outputs, and quality gates |
| The model can select its own interpretation | Material ambiguity becomes a question and a blocker |
| “It looks fine” | Every `Passed` status is linked to specific evidence |
| Focus on the happy path | Risks include boundaries, negative scenarios, states, retries, permissions, data, and export |
| Free-form QA conclusion | Contract → scenario → evidence → status / defect |

## What is in the package

| Asset | Purpose |
| --- | --- |
| [SKILL.md](SKILL.md) | Portable instructions for a compatible AI agent |
| [feature_brief.md](demo_case/feature_brief.md) | A fully specified synthetic feature: CSV task import |
| [ambiguous_brief.md](demo_case/ambiguous_brief.md) | A case where a good QA agent must stop and ask for a decision |
| [qa_context.md](demo_output/qa_context.md) | Verifiable contract ledger and risk map |
| [risk_based_test_plan.md](demo_output/risk_based_test_plan.md) | Traceable scenarios and independent test oracles |
| [qa_result.md](demo_output/qa_result.md) | An honest report with synthetic evidence and a defect draft |
| [skill_evaluation.md](validation/skill_evaluation.md) | How the skill’s own discipline was evaluated |

## Scope of this example

See [PUBLIC_DEMO.md](PUBLIC_DEMO.md). This package contains no commercial code, project terminology, user data, environment URLs, keys, screenshots, or real defects.
