# QA Context Brief — hard-stop example

**Feature:** synthetic CSV task import<br>
**Source:** [ambiguous_brief.md](../demo_case/ambiguous_brief.md)<br>
**Status:** `Needs answers`<br>
**Readiness:** `Not ready for plan`

## Captured contracts

| ID | Behavior | Source |
| --- | --- | --- |
| BC-001 | Preview labels rows as accepted/rejected and does not persist data before Confirm. | Brief |
| BC-002 | An invalid row does not change an existing task. | Brief |
| BC-003 | Re-importing data does not create duplicates. | Brief |
| BC-004 | Only a manager can perform Confirm. | Brief |

## Material question

**Q1.** In a mixed file with valid and invalid rows, should `Confirm` persist the accepted rows or reject the entire file?

**Why this blocks planning:** the answer changes the final task state, independent test oracle, rollback scenarios, and `Pass/Fail` criterion.

## Recommendation

`Blocked / No-Go`: do not create a test plan or release recommendation until the product owner records an answer to Q1. This is not a refusal to work; it prevents an invented expected result.
