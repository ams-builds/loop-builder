---
name: loop-builder
description: Guide a user through turning a workflow they already do repeatedly with Claude into a real Claude loop, a repeatable process with a trigger, a checklist, a state file, and a review point, instead of a repeated one-off prompt. Trigger this skill whenever the user asks to "build a loop," "automate this workflow," "turn this into a loop," "make this a repeatable check," asks what they can automate based on skills or workflows they already have, references worker/checker patterns, permission ladders, or persistent state for Claude, or asks whether they're "already running a loop." Also trigger if the user has several skills or standing instructions and asks what else could be automated safely.
---

# Loop Builder

A Claude loop is a small system around the model: something starts the run (trigger), Claude reads fixed context, does one constrained job (action), checks its own output against a concrete standard (verification), writes down what happened so the next run can pick up where this one left off (state), then stops, escalates, or repeats (decision). Missing any one of these six parts turns a "loop" back into a repeated prompt that happens to feel automated.

This skill walks a user through finding a good candidate, scaffolding the minimum viable version, testing it manually, and avoiding the mistakes that show up almost every time (below, under Common Failure Modes). Work through the steps in order. Do not skip to scaffolding before Step 2, and do not schedule or automate anything before Step 5.

## Step 1: Inventory what already exists

Ask the user what they already have Claude do repeatedly, standing instructions, skills, saved prompts, or things they paste into chat the same way each time. If they don't know where to start, ask:

- What do you ask me to check, review, or draft the same way more than once?
- What do you currently do by re-explaining the task from scratch each time?
- Do you have any saved skills, custom instructions, or prompt templates already?

List what comes back. Each item on that list is a loop candidate, not yet a loop.

## Step 2: Run the Loop Readiness Check on each candidate

For every candidate, ask these five questions. Answer honestly, don't force a yes.

1. **Does it repeat?** If it happens once, it doesn't need a loop.
2. **Can the result be verified?** Is there a concrete standard (a checklist, a rule, a test) or is "looks good" the only bar?
3. **Does Claude have enough context to act without you re-explaining it each time?** If the same background has to be retyped every time, that's a sign the loop needs a proper context file, not that it isn't loopable.
4. **Is there a clear stop condition?** "Keep going until it's good" is not one. "All checklist items pass" is.
5. **Is there a safe review point?** Can a human see the output before anything irreversible happens?

Rank candidates. A candidate that fails question 2 or 4 needs a sharper definition before it's worth building, not abandonment, just don't scaffold it yet.

Flag anything that touches sending messages, deleting data, or acting on someone else's behalf as needing a stricter permission ladder (see Step 4) regardless of how it scores above.

## Step 3: Pick one, don't build all of them at once

Even if several candidates pass, scaffold one first. A loop that hasn't been tested is not proof the pattern works; testing one properly teaches more than scaffolding five untested ones.

## Step 4: Scaffold the minimum viable loop

A minimum viable loop has three parts. Keep each one short.

**TASK**: the goal, what counts as done, and the scope boundary (what Claude may touch, what it may not).

**LOOP_INSTRUCTIONS**: the operating procedure. This is where most of the value lives:
- What to read before acting.
- The checklist, each item written so it can be answered PASS/FAIL, not "looks fine." If an existing skill or standing instruction already defines the rules (a style guide, a template, a set of banned patterns), the checklist should reference that source, not silently copy it into a second document that can drift out of sync.
- The **worker/checker split**: if the task involves both producing something and judging it, run those as two distinct passes. The checker does not rewrite, it reports PASS/FAIL per item and stops. A weak checker just agrees with the worker; a strong one has a standard to check against and says so explicitly for each item. **Be precise about what this is: by default it's a single agent running two sequential passes in one conversation, not multiple agents.** That's enough for most loops. It becomes genuinely multi-agent only if the platform's actual multi-agent features are used, for example Claude Code subagents, where the worker and checker run as separate agents with their own context windows rather than two passes in the same one. Don't describe a single-agent loop as multi-agent; the distinction matters to anyone technical evaluating the claim.
- A permission ladder starting point. Default to Level 1 (read-only) or Level 2 (drafts only, nothing sent or changed) for the first version, regardless of how trustworthy the task feels. Climb the ladder only after repeated clean runs, never on day one:
  1. Read-only analysis
  2. Draft output only
  3. Sandbox edits only
  4. Draft external actions (unsent messages, unopened tickets)
  5. Human-approved actions
  6. Fully automated low-risk actions
- A failure policy: what happens when verification fails (fix once and recheck, or stop and flag for human review). Never "keep trying indefinitely."
- A budget ceiling: a cap on iterations, time, or tokens, set before the first run, not adjusted mid-run because progress looks close. This is separate from the stop condition, a loop can be nowhere near its stop condition and still need to halt because it's burning effort on a problem it isn't solving.

**PROGRESS**: the state file. Current status, what happened last run, open items, blockers, what needs human review, what the next run should do, and a running review-notes log. This file must actually get written to after every run, not just created once and left templated, an unused state file is the single most common failure in a first loop.

## Step 5: Ask where state actually lives, don't assume

A loop's state must survive outside the current conversation, or it isn't a loop, it's a longer single conversation. Ask the user directly where they want it stored, don't default to whatever the last example used. People keep their working documents in very different places: Notion, Google Docs, Microsoft OneDrive, a wiki, a plain folder of files, a repository. Options, in order of how durable they are:

- A page or document in whatever the user already uses for notes, if Claude has direct read/write access there.
- A file in a repository or shared drive Claude can be pointed back to.
- Worst case: a file the user re-uploads each session, this works but relies on the user remembering to do it, flag this limitation explicitly if it's the only option available.

Do not leave loop files only in a chat sandbox or downloads folder with no plan to re-attach them. That state will not be there next session.

## Step 6: Test manually before automating anything

Run the loop by hand 3 to 5 times before considering any scheduled or triggered automation. Vary the input slightly each time (a new edge case, a deliberately planted error) and check:

- Did it actually read the state file, or start from zero?
- Did the checker catch what it was supposed to catch, or only what was easy to spot?
- Did it stay inside the permission boundary?
- Did it update state before stopping?

Log each test run's outcome in the PROGRESS file's review notes, including failures. A loop that fails and records the failure is useful. One that fails silently is not.

## Step 7: Confirm it's ready

A loop is ready when: the user can trigger it in plain language (no special command needed unless the platform provides one), the checklist is being applied mechanically rather than skimmed, state updates every run without being asked, and at least a few manual runs came back clean. At that point, tell the user plainly what phrase or trigger to use from here on, and what it will and won't do on its own (per the permission ladder level it's currently at).

## Step 8: Validate the gate, not just the output

A verification gate can be wrong in two ways: green for the wrong reason, or too shallow to catch the real failure. Before trusting a new checklist or test as a gate:

- **Run it red-first.** Confirm the gate actually fails on the unfixed version, for the right reason, before relying on it to confirm success. A gate that passes on broken input was never checking anything.
- **Keep the gate outside the loop's own writable scope.** If a loop can edit the checklist, the test script, or the rubric it's judged against, it can make a failing run pass by weakening the check instead of fixing the underlying problem. Diff the gate itself after a run if there's any doubt.
- **For anything subjective or visual** (writing quality, design, "does this look right"), use a judge pattern: a separate pass, not the one that produced the output, scores the result against a written rubric and returns pass/fail. The author judging its own work is the single most common way a checker quietly becomes a rubber stamp.

## Coding loops specifically

Everything above applies to code, with a few code-specific additions:

- **Verification is a test suite, build, or type check**, not a read-through of the diff. Prefer something that actually runs the code (tests, a smoke run) over lint-only, lint passing says nothing about whether the code works.
- **Isolate changes in a branch or worktree**, never the main branch, until a human approves the merge.
- **Use a command allowlist** if the loop can run shell commands: list exactly which commands are permitted (test runners, linters, `git status`/`git diff`) and block package installation and arbitrary script execution by default.
- **Add a stall check.** If several consecutive attempts fail in the same way, stop and escalate rather than retrying indefinitely, an unbounded retry loop burns time and tokens on a problem it isn't solving.

## Sensitive data and standing access

If a loop touches anything beyond local files, connectors, credentials, other people's data, add these before granting access:

- The loop must never read, copy, or expose secrets, API keys, credentials, or personal data beyond what the task strictly needs, even incidentally through a log or report.
- Keep a short audit entry per run for connected loops: what was read, what was changed, what was flagged for human review. This matters most exactly when nothing looks wrong, an unreviewed audit trail is only useful before something goes sideways, not after.
- Re-check a loop's actual permissions every so often, or whenever a new tool is added. Access tends to expand one convenience at a time until it's broader than anyone intended.

## Keeping the loop in sync with its source

If a loop's checklist is drawn from a skill, a style guide, or any other rule set the user maintains separately, those two copies can quietly disagree the moment one changes and the other doesn't. This is drift: not a bug in either document, just two copies of the same rule falling out of step over time. It matters because a loop checking against a stale checklist will pass things the current rules would fail, and the gap is invisible until someone notices by chance.

- **Name this risk to the user explicitly** for any loop whose checklist references an external skill or rule set. A loop whose rules are entirely self-contained (a database query, a fixed threshold) doesn't have this problem, only flag it where it's real.
- **The fix is a scheduled re-check, not vigilance.** Manually remembering to sync two documents works until it doesn't. Set up a periodic check that compares the loop's checklist against its source and updates the checklist if they've diverged. How to do this depends on the platform, it is not one thing everywhere:
  - **Claude.ai / Claude apps**: use Cowork's Scheduled Tasks feature, it has a built-in scheduler and can read/write the same files or connectors the loop uses.
  - **Claude Code**: there is no scheduling UI built into an interactive session. Run Claude Code in headless mode (`claude -p "your prompt"`, non-interactive, no approvals) and trigger that command from an OS-level scheduler, cron, a systemd timer, or a CI pipeline like GitHub Actions. This is the standard way Claude Code automation runs unattended; treat it as infrastructure you set up once, not a Claude Code feature you toggle on.
  - If neither is available, this step doesn't happen automatically, say so plainly rather than implying it does. A manual "please check for drift" reminder is a weaker but honest fallback.
- **Scope the scheduled check narrowly.** It should update only the checklist and a clearly-marked snapshot of what it found, never the loop's run history or decisions log, those are for actual runs, not sync checks.
- **State the actual latency plainly.** A daily check means at most a day of drift, not eliminated drift. Say so, so the user isn't surprised that a same-day change takes a few hours to catch up.

## Common Failure Modes

These come up often enough to check for explicitly, not just in theory:

- **Skimming instead of mechanically checking.** A checklist item like "no instances of X" needs a literal scan, not a read-through. Skimming reliably misses roughly half of what's there on anything longer than a paragraph.
- **State file never gets written to.** Easy to build, easy to forget to update. If the PROGRESS file still says "not yet run" after several real runs, the loop isn't functioning as a loop yet, regardless of how good the checklist is.
- **Duplicating rules instead of referencing them.** If the loop's checklist copies content from an existing skill or style guide word for word, the two will drift out of sync the first time either changes. State the relationship explicitly and update both together.
- **Skipping the permission ladder because the first output looked fine.** Reliability shows up across repeated runs, not a single one. Autonomy is earned through repetition, never assumed after one good impression.
- **Assuming tool access matches what the instructions assume.** If a loop's instructions assume a capability (a particular query tool, an integration, a paid tier) that turns out to be unavailable, fall back honestly and say so in the state file rather than silently producing a partial result.
- **Treating an ambiguous or oddly-shaped record as if it fits the pattern.** Real data has outliers, a database row that's actually a stale note, an entry with blank fields where content is expected. Check anything that looks stale or unusual before drafting a follow-up or flagging it as if it were a normal case.
- **Fixing a mistake only inside one loop's state file.** A correction logged in PROGRESS helps the next run of that one loop. Writing the same correction into the loop's instructions, or into a skill or standing instructions the user has elsewhere, helps every future loop that could hit the same mistake. When a correction is general rather than specific to this one run, push it up a level instead of leaving it buried in a single state file.
