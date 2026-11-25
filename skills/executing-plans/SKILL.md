---
name: executing-plans
description: Use when you have a written implementation plan to execute in a separate session with review checkpoints
---

# Executing Plans

## Overview

Load plan, review critically, execute all tasks, report when complete.

**Announce at start:** "I'm using the executing-plans skill to implement this plan."

## The Process

### Step 1: Load and Review Plan
1. **Determine workspace strategy — the default is NO worktree.** A plan that names a branch strategy ("new branch", "feature branch", "on master") is not, by itself, a request for an isolated workspace. Branches and worktrees are different things, and most plans mean only the former.
   - **Plan/spec/prompt names a branch, no worktree mentioned?** Create/checkout that branch in the current workspace (`git checkout -b <branch>`).
   - **Nothing specified, or told to work on master/main?** Work in place on the current branch, with your human partner's explicit consent for master. Never start implementation on master without it. No worktree, no new branch, unasked.
   - **Already in a linked worktree** (`git rev-parse --git-dir` differs from `--git-common-dir`, and it isn't a submodule)? Continue there.
   - **Worktree explicitly requested** (the invoking prompt, plan or spec says "worktree" or "isolated workspace", not just "branch")? Create one with the native worktree tool (`EnterWorktree`), falling back to `git worktree add` only if you have none.
2. Read plan file
3. Review critically - identify any questions or concerns about the plan
4. If concerns: Raise them with your human partner before starting
5. If no concerns: Create todos for the plan items and proceed

### Step 2: Execute Tasks

The plan gives structure, interfaces and decisions, not code. You write the implementation. Read the spec(s) for intent and the plan's **Key Decisions & Gotchas** before starting.

For each task:
1. Mark as in_progress
2. Write the tests for the listed behaviours first, watch them fail, then implement (TDD)
3. Honour the task's Interfaces exactly. Later tasks depend on them.
4. Run the task's **Done when** verification
5. Mark as completed

**When reality disagrees with the plan:** if it's small and local (a file lives elsewhere, a helper already exists, a signature needs one extra parameter), adapt and note it for Implementation Notes. If it changes an interface other tasks depend on, contradicts the spec, or invalidates a Key Decision, stop and ask.

### Step 3: Manual Verification Checklist

Once all tasks pass, work through the plan's **Manual Verification Checklist**. Who runs it:

- **Your human partner said they want to do it** (in the invoking prompt or at any point): hand it over in chat. Don't just point at the plan file. For each item give the setup it needs (shared setup once at the top, with the exact commands), the exact steps, and what they should see. Offer to start long-running setup yourself, such as dev servers or seed scripts. Then wait for their results.
- **Otherwise, run it yourself** with the tool each item names: Playwright for web UIs, curl or a script for APIs, the CLI for tools. Actually perform each step and compare against **Expected**. Don't infer a pass from the code. Then hand the `[user-only]` items to your human partner in chat in the same format as above, and wait for their results.

Report results as a table: item, pass/fail, and what you observed when it failed. Fix failures (back through TDD for the affected task), then re-run the failed items and anything the fix could affect. If an item is wrong rather than the code (for example, the spec changed), say so and don't bend the code to fit it.

### Step 4: Write Implementation Notes

Before finishing, fill in the plan's **Implementation Notes** section (add it if missing). Cover deviations from the plan and why, final names/interfaces that differ from the plan, surprises, and anything the next stage's planner should know. Aim for under ~40 lines. The planning session reads this instead of the whole diff, so make it accurate. Include it in the final commit.

### Step 5: Complete Development

After all tasks complete and verified, run the full test suite, then follow whatever the project's workflow says happens next (CLAUDE.md, the plan, or the prompt that invoked you): commit, push, open a PR, and so on. If none says, commit and ask your human partner what to do with the branch.

## When to Stop and Ask

Stop and ask, rather than guess, on a blocker (missing dependency, unclear instruction, repeated verification failures) or a critical gap in the plan. Don't force through blockers.

Go back to Step 1 when your partner updates the plan, or the approach needs rethinking.
