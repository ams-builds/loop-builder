# Jargon Buster

Plain-English explanations of the technical words used in this project. The README avoids these terms where it can; this file is where the precise vocabulary lives, for anyone who wants it.

**Action**
The actual work getting done, the one job this run of the loop is for.

**Claude Code**
A version of Claude built for developers, used from the terminal or an editor. It doesn't have a built-in scheduler the way Cowork does.

**Context**
The fixed instructions Claude reads before doing the job, so it doesn't need re-explaining each time.

**Cowork**
A version of Claude built for non-developers to delegate ongoing work, including running tasks on a schedule without you opening a chat each time.

**Cron / scheduler**
A way computers have long used to run a command automatically at set times, without a person present. Claude Code relies on one of these (cron, a systemd timer, or a CI pipeline like GitHub Actions) for scheduling, since it doesn't have one built in.

**Decision**
What happens after checking the work: keep going, stop, or ask a human.

**Drift**
When two copies of the same rule slowly stop matching each other, for example, a rule changes in one place but nobody updates the other place using the old version.

**Escalation**
When something's unclear or stuck, Claude stops and asks a person instead of guessing.

**Gate**
The specific check a loop uses to decide pass or fail, a test, a rule, a checklist item.

**Headless mode**
A way of running Claude Code without the interactive chat window, you give it one instruction, it runs, and it exits. This is what makes it possible to trigger Claude Code from outside a normal conversation.

**Loop**
A task that repeats and checks itself, instead of you asking Claude to do the same thing from scratch every time.

**Permission level** (also called the "permission ladder")
How much Claude is allowed to do on its own before checking in. Starts small, read-only or drafts only, and only grows once things have been tested and trusted.

**Readiness check**
5 quick questions used to check whether a task is actually a good fit for a loop before building one: does it repeat, can it be checked, is there enough background info, is there a clear stopping point, and can a human see the result before anything risky happens.

**Scheduled Tasks**
A feature in Cowork and the Claude desktop app that runs a prompt automatically on a repeating schedule, daily, weekly, whatever you set, without you triggering it by hand.

**State**
A saved note of what happened last time, so the next run doesn't start from zero.

**Trigger**
Whatever starts a run. Usually just you asking. Sometimes a schedule, like "every day."

**Verification** (also called "checking the work")
Comparing the result against a clear standard, and getting a straight yes or no, not a vague "looks fine."
