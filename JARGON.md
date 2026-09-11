# Jargon Buster

Plain-English explanations of the technical words used in this project. The README avoids these terms where it can; this file is where the precise vocabulary lives, for anyone who wants it.

**Loop**
A task that repeats and checks itself, instead of you asking Claude to do the same thing from scratch every time.

**Trigger**
Whatever starts a run. Usually just you asking. Sometimes a schedule, like "every day."

**Context**
The fixed instructions Claude reads before doing the job, so it doesn't need re-explaining each time.

**Action**
The actual work getting done, the one job this run of the loop is for.

**Verification** (also called "checking the work")
Comparing the result against a clear standard, and getting a straight yes or no, not a vague "looks fine."

**State**
A saved note of what happened last time, so the next run doesn't start from zero.

**Decision**
What happens after checking the work: keep going, stop, or ask a human.

**Escalation**
When something's unclear or stuck, Claude stops and asks a person instead of guessing.

**Permission level** (also called the "permission ladder")
How much Claude is allowed to do on its own before checking in. Starts small, read-only or drafts only, and only grows once things have been tested and trusted.

**Readiness check**
5 quick questions used to check whether a task is actually a good fit for a loop before building one: does it repeat, can it be checked, is there enough background info, is there a clear stopping point, and can a human see the result before anything risky happens.

**Drift**
When two copies of the same rule slowly stop matching each other, for example, a rule changes in one place but nobody updates the other place using the old version.

**Gate**
The specific check a loop uses to decide pass or fail, a test, a rule, a checklist item.
