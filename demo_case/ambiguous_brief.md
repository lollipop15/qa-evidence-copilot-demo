# Synthetic brief: CSV task import — intentionally incomplete

A manager uploads a CSV with the columns `task_id`, `title`, `priority`, and `due_date`.

`Preview` classifies rows as `accepted` or `rejected`. After `Confirm`, tasks are created or updated; `Export` downloads the current task set.

- `task_id` and `title` are required.
- `priority` accepts `low`, `medium`, and `high`.
- `due_date` uses `YYYY-MM-DD` and cannot be more than 365 days in the future.
- Re-importing data does not create duplicates.
- Invalid rows do not change existing data.
- `Confirm` is available only to the `manager` role.
- The interface supports English and Russian.

## Why this brief is deliberately ambiguous

It does not specify what `Confirm` should do when one file contains both valid and invalid rows: reject the entire file or apply only the valid subset. Both approaches are technically possible, but they create opposite expected results and different recovery rules.

The correct Evidence-First QA Copilot behavior is to produce a `QA Context Brief` with `Needs answers`, rather than invent a rule and continue with a test plan.
