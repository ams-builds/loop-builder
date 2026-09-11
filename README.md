# loop-builder

A Claude skill that walks you through turning a workflow you already repeat with Claude into a real **Claude loop**, a repeatable process with a trigger, a checklist, a state file, and a review point, instead of a repeated one-off prompt.

## What it does

Ask Claude to build a loop, or ask what you could automate based on skills or workflows you already have, and this skill guides Claude through:

1. Inventorying what you already do repeatedly
2. Checking each candidate against a 5-question readiness test
3. Scaffolding the minimum viable loop (task, instructions, state file)
4. Recommending a safe starting permission level (read-only or draft-only)
5. Deciding where the loop's state should actually live so it survives between sessions
6. Testing manually before anything gets automated
7. Confirming when it's actually ready to use

It also encodes a handful of failure modes that show up constantly in first loops (skimmed checks, unused state files, duplicated rules that drift out of sync) so Claude checks for them rather than repeating them.

## How it works

*Diagrams below follow the visual language of [cathrynlavery/diagram-design](https://github.com/cathrynlavery/diagram-design): one accent color, editorial type roles, 1px hairlines, no shadows, a 4px grid. Built as standalone SVG rather than through that skill directly, since it's a Claude Code plugin and this repo targets multiple Claude surfaces.*

![Loop anatomy flowchart: trigger, context, action, verification, state, decision, with a fail branch back to action and a continue branch back to trigger](assets/loop-anatomy-flowchart.svg)

The six-part cycle every loop in this skill is built around. A fail on verification loops back to a single fix-and-recheck pass, not indefinite retries. A stalled or ambiguous decision escalates to a human instead of guessing.

![Architecture diagram of four illustrative loops fed by one scheduled sync task, each reading its own skill or database source](assets/four-loops-architecture.svg)

An illustrative example: 4 loops sharing one hub page. 2 of them (dashed boxes) pull rules from a skill and carry drift risk if that skill changes; 2 of them (solid muted boxes) just query a database and have no such risk. The scheduled task at the top is what makes the first 2 self-correcting instead of relying on someone remembering to sync them by hand.

## How to install

**Claude.ai / Claude apps**: upload `SKILL.md` as a custom skill, or place this folder where your Claude setup looks for skills.

**Claude Code**: drop this folder into your skills directory (commonly `~/.claude/skills/` or your project's `.claude/skills/`).

## How to use it

Once installed, talk to Claude normally:

- "Help me build a loop out of my [X] workflow"
- "What could I turn into a loop based on the skills I already have?"
- "Am I already running a loop?"

The skill triggers on its own; you don't need to invoke it by name.
