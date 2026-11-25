---
name: stress-testing-plans
description: Use after writing-plans saves a plan, when your human partner accepts the optional multi-faceted review, or when they ask to stress-test, harden or review a spec and plan before execution
---

# Stress-Testing Plans

## Overview

A plan written by one agent sees the feature the way its author intends it to be used. This skill sends the spec and plan to independent specialist reviewers, each looking through one lens: design choices, edge cases, maintainability, scale, security and (when there is a GUI) UI/UX. They write reports. You wait for all of them, then compile one verdict for your human partner.

Plans hold key decisions, not code. So every reviewer imagines the **default, most obvious implementation** a capable engineer would write from the plan, and asks where *that* goes wrong. Not the worst possible implementation where every mistake happens at once.

**Announce at start:** "I'm using the stress-testing-plans skill to run the multi-faceted review."

## When to Offer It

writing-plans offers this at handoff as optional and recommended. Run it only after your human partner says yes. It costs several Opus-tier subagent runs, so it's their call.

## Step 1: Gather Inputs

- **Spec path(s)**: this stage's spec, plus the parent spec if multi-stage.
- **Plan path**.
- **Project root** (reviewers read the existing code to ground findings).
- **Review directory**: `<your scratchpad directory>/plan-review-<plan-name>/` if your environment gives you a scratchpad, else `<system temp dir>/plan-review-<plan-name>-<YYYYMMDD-HHMM>/`. Absolute path. Never inside the project.
- **Ground rules path**: the absolute path of `reviewer-ground-rules.md` in this skill's directory.
- **Has a GUI?** Yes if the plan builds or changes a website, web app, desktop/mobile app, or any screen a human looks at. A CLI, library, API or backend service is no.
- **Stakes for edge cases**: `xhigh` if the plan touches money, auth/permissions, user data that can be lost or corrupted, concurrency, sync/offline, or anything irreversible. Otherwise `high`.

## Step 2: Dispatch All Reviewers at Once

Send every Agent call in **one message**, all in the background. Use the plugin's agent types (clean subagents, never forks). Each gets the same dispatch prompt (below) with its own report filename.

| Lens | `subagent_type` | Model / effort (set by the agent file) | Report file |
|---|---|---|---|
| Design choices | `pro-skills:plan-design-reviewer` | Opus 5.5, medium | `design.md` |
| Edge cases | `pro-skills:plan-edge-case-reviewer` | Opus 5.5, high; pass `effort: "xhigh"` when stakes are especially high | `edge-cases.md` |
| Maintainability | `pro-skills:plan-maintainability-reviewer` | Sonnet 5.5, high | `maintainability.md` |
| Robustness & scale | `pro-skills:plan-scale-reviewer` | Opus 5.5, high | `scale.md` |
| Security | `pro-skills:plan-security-reviewer` | Sonnet 5.5, high | `security.md` |
| UI/UX (**GUI only**) | `pro-skills:plan-ux-reviewer` | Sonnet 5.5, high | `ux.md` |

Dispatch prompt:

```
Review this spec and plan through your lens.

Ground rules (read first, follow exactly): [GROUND_RULES_PATH]
Spec(s): [SPEC_PATHS]
Plan: [PLAN_PATH]
Project root: [PROJECT_ROOT]
Write your report to: [REVIEW_DIR]/[REPORT_FILE]

Context you can't get from the files: [anything your human partner said that
shapes this plan and isn't written in the spec — constraints, preferences,
"we'll never have more than 50 users", target platforms. "None" if nothing.]
```

Don't paste the spec or plan into the prompt. Reviewers read the files.

## Step 3: Wait for Every Reviewer

**Do not read, summarise or act on any report until every dispatched reviewer has finished.** Not after the first one, not after "most of them". Reviewers overlap, and a finding only means something next to the others. While you wait, say which ones are still running if your human partner asks. Don't predict their findings.

If a reviewer fails outright (crash, no report), re-dispatch it once. If it fails again, compile without it and say which lens is missing.

## Step 4: Compile

Follow `compiling-reports.md` in this skill's directory. In short: verify each finding against the spec, plan and code, drop duplicates and worst-case pile-ups, then sort what's left into three buckets:

1. **Apply**: true, consistent with every design choice and with what your human partner asked for. Add these to the plan's **Key Decisions & Gotchas** right away.
2. **Your call**: probably true, but it might cut against what your human partner wants (scope, cost, a dependency, a UX tradeoff). Ask.
3. **Design choices to re-evaluate**: decisions a reviewer pushed back on, each with the reason it might not be the best choice and the alternative.

Then hand back to writing-plans' execution options.

## Red Flags

| Thought | Reality |
|---|---|
| "Design and edge cases are back, I'll start compiling" | Wait for all of them. Always. |
| "I'll let the reviewers edit the plan directly" | They write reports only. You are the only one who edits the plan, after compiling. |
| "This finding sounds right, apply it" | Check it against the code and spec first. Reviewers guess at an implementation that doesn't exist yet. |
| "Apply every finding, more gotchas is safer" | A plan stuffed with speculative gotchas blows the size budget and buries the real ones. |
| "It's a backend, but I'll run UX anyway" | No GUI, no UX reviewer. |
| "I'll fork this conversation to the reviewers" | Clean subagents only. They need the files, not your reasoning, so they aren't anchored by it. |
