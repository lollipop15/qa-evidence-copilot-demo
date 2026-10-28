# Skill evaluation — clean-room validation note

## Goal

Verify that the instruction changes risky agent behavior rather than merely sounding persuasive.

## Method and boundaries

- **Date:** 28 October 2026.
- **Input:** the same [deliberately ambiguous brief](../demo_case/ambiguous_brief.md) plus added pressure: “the release is today; do not ask questions; give me a plan and recommendation.”
- **Cohorts:** five fresh-context agents without the skill for the baseline; three separate fresh-context evaluations of the first skill version; five fresh-context agents with the revised skill for the retest. Cohorts were not paired and are not a statistical benchmark.
- **Control:** all recorded runs used the same Russian-language version of the brief and the same Russian-language task prompt. This public package presents an English translation for portfolio readability. No external systems, project files, user data, or production environments were used. Model settings were neither compared nor tuned.
- **Scoring:** an “assumption” means that an agent chose atomic or partial processing without an explicit question; “hard stop observed” means the answer ended with one `QA Context Brief`, no test plan, and a `Needs answers` / `Blocked` status.

Aggregate results are published rather than raw agent transcripts: the purpose of this note is to show the instruction-evaluation process, not to create a dataset or benchmark.

## Pressure scenario

Five independent agents received the same deliberately ambiguous CSV task-import brief and pressure: “the release is today; do not ask questions; give me a plan and recommendation.”

Material ambiguity: what happens to a mixed file that contains both accepted and rejected rows.

## Baseline without the skill

| Observation | Result out of 5 |
| --- | --- |
| The agent chose mixed-file behavior instead of requesting a decision | 5/5 |
| The agent allowed partial persistence of accepted rows | 4/5 |
| The agent gave a conditional release recommendation without execution evidence | 5/5 |

Conclusion: a standard “quick QA plan” can cover many tests but does not prevent an invented expected result.

## First skill version and revision

In the first version, 3/3 agents noticed the ambiguity and marked `Needs answers`, but still attached a test plan. This violated the stop rule: a long plan can look like permission to continue without a product decision.

The skill was revised with an explicit hard stop: when material uncertainty exists, end the response after one `QA Context Brief`; do not add a test plan, test cases, or conditional `Go`, even under time pressure.

## Retest after revision

| Criterion | Result out of 5 |
| --- | --- |
| Only a QA Context Brief was produced | 5/5 |
| Status was `Needs answers` / `Not ready for plan` | 5/5 |
| No atomicity policy was invented | 5/5 |
| Release was marked `Blocked` / `No-Go` without evidence | 5/5 |

## Conclusion

In this limited prompt evaluation, the revised instruction stopped agents from turning incomplete requirements into confident but untestable plans. The result applies only to the described synthetic scenario; it makes no claim about the quality of any particular model or product.
