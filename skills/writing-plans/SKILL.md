---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

The spec says **what** to build and **why**. The plan says **how it fits together**: the file layout, task order, the interfaces between tasks, and the key implementation decisions and gotchas a capable engineer might not think of right away. The executor writes the code. The plan does not.

Write for a skilled engineer in a fresh session. They have full access to the codebase plus the spec(s) and this plan, but none of this conversation's context. Give them the decisions they couldn't reconstruct by themselves. Leave out anything they'd do correctly without being told.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Save plans to:** `.pro-skills/plans/YYYY-MM-DD-<feature-name>.md`
- (User preferences for plan location override this default)

## Planning Is Not Implementing (hard rule)

Do **not** write implementation code while planning: not in the plan, not in a scratchpad, not in temp files, not "just to check it works". The executor runs in a fresh session and will never see it, so it's wasted tokens and it fills up this session's context. This session needs to stay light, because your human partner comes back to it to plan the next stage.

Allowed and expected:
- **Targeted reading** of existing code to ground decisions (where things live, existing patterns, current signatures). Read what you need, not whole subsystems.
- **A minimal probe** only when a real unknown blocks a decision (e.g. "does this library version support X?"). Keep it to a few lines, record the conclusion in the plan in one sentence, and move on.

If you notice you're writing function bodies, stop. Write down the decision behind them instead.

## Size Budget

The executor must be able to read the whole plan in **one** Read. Aim for roughly 200–500 lines. If you go over, you're writing code the executor would write anyway, so cut it. If it's still too long, the stage is too big. Suggest splitting it.

## Scope Check

If the spec covers multiple independent subsystems, it should have been broken into stage specs during brainstorming. If it wasn't, suggest one plan per stage. Each plan should produce working, testable software on its own.

## What Code Belongs in a Plan

Only these, and kept short:
- **Interfaces that cross task boundaries**: signatures, types, data shapes, schema. These are the contracts that let tasks fit together.
- **Exact values**: config keys, env vars, CLI flags, file formats, user-facing strings and error messages the spec fixes.
- **A short sketch (≤ ~15 lines)** for a genuinely non-obvious algorithm or tricky API usage, when prose would be ambiguous.

Not in a plan: function bodies, full test code, boilerplate, imports, anything the executor would write the same way unprompted. Describe tests as **behaviours to verify**, not as test code.

## Task Right-Sizing

A task is the smallest unit that carries its own test cycle and is worth a reviewer's gate. Fold setup, configuration, scaffolding and docs into the task whose deliverable needs them. Split only where a reviewer could meaningfully reject one task while approving its neighbour. Each task ends with an independently testable deliverable.

## Commit Strategy

Do not commit after every task. Commit once, at the end, when everything is implemented and passing. Exception: if the plan bundles genuinely distinct features, each feature gets its own commit at the end of its final task, so at most two commits.

## Plan Document Template

````markdown
# [Feature / Stage Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: pro-skills:executing-plans. This plan gives structure and decisions, not code. You write the implementation (TDD per task). Track tasks with the checkboxes. After all tasks, run the **Manual Verification Checklist** (or hand it to the user if they asked to do it), then fill in **Implementation Notes**.

**Goal:** [One sentence]
**Specs:** [Path to this stage's spec; path to parent spec if multi-stage]
**Architecture:** [2–4 sentences on the approach]
**Tech Stack:** [Key technologies/libraries]

## Global Constraints
[Project-wide requirements from the spec, exact values verbatim, one line each. Every task includes them.]

## File Map
[Each file created/modified, one line on its responsibility.]

## Key Decisions & Gotchas
[The core of the plan, as bullets: non-obvious choices and why, traps in existing code, ordering constraints, edge cases the spec implies, probe results.]

## Tasks

### Task N: [Component Name]
**Files:** `path/a.py` (create), `path/b.py` (modify: the X handler), `tests/test_a.py`
**Interfaces:**
- Consumes: [exact signatures from earlier tasks]
- Produces: [exact signatures later tasks rely on]

**Build:** [2–6 bullets: what this task does, including any decision specific to it]

**Tests (behaviours):**
- [given/when/then style, one line each, including key edge cases]

**Done when:** [command(s) to run and the expected outcome]

- [ ] Task N complete

[...repeat...]

## Manual Verification Checklist
[End-to-end checks of the finished stage as a user would exercise it. See below.]

**Shared setup:** [exact commands from a clean checkout to a running system]

#### MV-1: [What this check proves]
- **Setup:** [state needed beyond shared setup, or "none"]
- **Steps:**
  1. [Exact action: URL, element, request, command]
  2. [...]
- **Expected:** [Exact text, status, fields, log line. Also what must NOT happen]
- **How the executor runs it:** [Playwright / curl / CLI / script], or `[user-only]` with the reason

[...repeat...]

## Out of Scope
[What belongs to a later stage or not at all.]

## Implementation Notes
_Filled in by the executor: deviations and why, changed names/interfaces, surprises, notes for the next stage's planner._
````

## Manual Verification Checklist

Every plan includes one. Unit tests prove the pieces work. This checklist proves the stage works the way the spec intends when someone actually uses it. Either the executor runs it (Playwright for websites, HTTP requests for APIs, the CLI for tools, or whatever fits what's being built) or your human partner runs it by hand. So write every item so a person with no context could follow it exactly.

Be thorough:
- **Cover every user-facing behaviour in the spec:** the main flows, then the edge cases and failure paths (bad input, empty states, permissions, interrupted operations, persistence across restarts, whatever applies).
- **Check integration points unit tests can't reach:** real config, real database, the real browser, interaction with the previous stage's features (regressions).
- **Be exact:** literal URLs, selectors or visible button labels, request bodies, commands, expected text and values. "Check that it works" or "verify the page looks right" are failures.
- **One behaviour per item,** so a failure points at one thing.
- **Mark `[user-only]`** on anything that genuinely can't be automated, and say why.

The checklist doesn't count toward the size budget, but keep each item tight. Thorough means complete coverage, not long prose.

## No Vagueness

A plan with little code still has to be precise. Plan failures: "TBD"/"TODO"; "handle errors" or "add validation" without saying which; tests listed as "test the above"; interfaces used but never defined; a decision the spec left open that the plan leaves open too.

## Self-Review

After writing the plan, check it against the spec. Do this yourself, inline. Don't dispatch a subagent.

1. **Spec coverage:** every spec requirement maps to a task. Add tasks for gaps.
2. **Interface consistency:** names and types in "Consumes" match the earlier "Produces".
3. **Vagueness scan:** fix anything under No Vagueness.
4. **Over-specification scan:** delete any code the executor would write the same way unprompted. Check the size budget.
5. **Checklist coverage:** every user-facing behaviour and failure path in the spec has a Manual Verification item, and every item has exact steps and an exact expected result.

Fix inline and move on.

## Execution Handoff

After saving the plan, first offer the optional review:

**"Plan saved to `.pro-skills/plans/<filename>.md`. Before executing, I recommend a multi-faceted review: independent reviewers check the spec and plan for design choices, edge cases, maintainability, scale, security[, and UI/UX]. Gotchas they find get added to the plan, and anything that changes scope or a design choice comes back to you. It runs several subagents, so it takes a while and costs more tokens. Want me to run it?"**

**If yes:** REQUIRED SUB-SKILL: pro-skills:stress-testing-plans. When it's finished, come back here and offer the execution options below.

**If no:** go straight to the execution options:

**"Two options:**

**1. Fresh Session (recommended)**: start a new session, attach the spec(s) and this plan, and ask for it to be implemented with pro-skills:executing-plans. When it's merged, come back here and I'll read the plan's Implementation Notes and the actual code to plan the next stage.

**2. Inline Execution**: I execute it in this session with executing-plans.

**Which approach?"**

**If Fresh Session:** do nothing more here. Tell your human partner which files to attach and wait.

**If Inline:** REQUIRED SUB-SKILL: pro-skills:executing-plans.

## Returning After Execution (multi-stage work)

When your human partner comes back after a stage is merged, catch up cheaply before planning the next stage. Read the plan's **Implementation Notes** first, then `git log`/`git diff --stat` for the merged range. Only then read the specific files the next stage touches. Don't re-read the whole implementation.
