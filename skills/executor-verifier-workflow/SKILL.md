---
name: executor-verifier-workflow
description: Use for big changes that go brainstorming → spec → plan → execute, in every project. Load when offering the execution choice after writing-plans or the stress-test review, when starting an executor session (Sonnet 5.5, high effort) to run a plan, or when starting a clean-context verifier session to manually check finished work.
---

# Executor and verifier sessions

Applies to big changes that start with `pro-skills:brainstorming` (spec → plan → execute), in every project.

## Rule 1: Executor sessions run on Sonnet 5.5 at high effort, set explicitly

- When starting an executor session (`start_session` for executing a plan), always pass `model` set to Sonnet 5.5 (`claude-sonnet-5-5`) and `effort: "high"`. Never let it inherit model or effort from me.
- There's a bug: a session that isn't at high effort or above can't start one at high. So before starting an executor, I must be at high effort myself.
- When I offer the execution choice (after writing-plans, or the stress-test review), the button for a fresh session reads exactly **`Fresh session, I've turned you to high effort`**, to remind the user to raise my effort first. Skip the reminder only when I'm already on high (big features usually are).

## Rule 2: A fresh verifier does the manual checks, not the executor

- After the executor finishes the plan's tasks, a **separate, fresh verifier session** does all the manual verification: Sonnet 5.5 (`claude-sonnet-5-5`), `effort` `"medium"` for small or medium changes and `"high"` for big ones. Set both explicitly. Either I or the executor start it.
- The verifier gets **minimal context**. The executor (or I) relays only what the verifier needs to run the checks:
  - the setup (commands, servers, accounts, seed data, ports),
  - the exact steps,
  - the expected result of each check.
- The verifier must **not** read the plan, the spec or the diff, and the brief carries little or no code. A model that reviews its own work with the full context tends to see what it intended and calls everything fine. A clean-context verifier sees what is actually there. This also saves usage.
- The verifier uses the Claude Desktop built-in browser for UI checks, and reports back to the executor with pass, fail or couldn't-set-up per check, plus evidence.

**Why:** the executor reviewing its own work with full context misses flaws that a clean-context verifier catches, and a minimal-context verifier on Sonnet costs far less.

## Rule 3: If starting a session is refused, ask. Never fall back to a subagent

When a workflow step requires launching a session (an executor, or a verifier started by an executor) and the auto mode permission check blocks it because the user hasn't told me directly to start one, stop and ask in chat whether I can start the session. Wait for a yes, then start it. Do not substitute a subagent.

- **Away mode is the exception.** The user approved the sessions up front (see the `away-mode` skill), so a refusal means something unplanned came up. Ask anyway if the user is reachable. Otherwise record it under "Decisions made while you were away" and still don't substitute a subagent.

**Why:** Subagents have no Stop button, so only I can stop them, which is sometimes undesirable. The user also has much less control over them: model, effort level, permission mode.

## The full workflow for big changes

1. Brainstorm and plan with the user (usually Opus 5.5 medium). At execution time, remind the user to turn me to high effort, unless it's a big feature and I'm already on high.
2. Start a Sonnet 5.5 high executor session to execute the plan (Rule 1).
3. When it's done, I or the executor start a Sonnet 5.5 high (or medium) verifier with a clean context. It verifies the app (Claude Desktop browser for UI) and reports back to the executor (Rule 2).
4. The executor fixes, then asks the verifier to redo every non-passing check (failed, couldn't set up, etc.) plus a few passing ones for regression, and to report again.
5. The executor fixes any remaining issues, opens a PR ready for review, and follows [CodeRabbit Plus PR reviews](#coderabbit-plus-pr-reviews).
6. The executor evaluates the posted findings, fixes the valid ones, and pushes the fixes. If there were more than two findings, follow the PR review process for another round.
7. The executor addresses any valid findings from the second round.
8. It makes a docs commit and reports to me.
9. The user checks for polish and tells me what to improve. I relay it to the executor, it fixes, and the user re-checks.
10. Merge. Unless the project has explicit instructions that say something different, the rule is to
    - Squash-merge it and delete the branch remotely in the same `gh` command
    - Delete the branch locally.
    - Fast-forward the local default branch.

## CodeRabbit Plus PR reviews

CodeRabbit Plus posts its review on the GitHub pull request. Check project memory for whether automatic reviews are enabled. Automatic reviews are off by default.

- **Automatic reviews on.** Opening a PR ready for review triggers a full review. Pushing fixes to the PR branch triggers a follow-up review.
- **Automatic reviews off.** Apply the GitHub label `coderabbit-plus:full` to the PR for the first review. After pushing fixes, apply `coderabbit-plus:commit` to review the latest commit. Use `coderabbit-plus:full` if the fixes span multiple commits and need to be reviewed together. If the desired label is already on the PR, remove it and reapply it after the push.
- **When the review arrives.** If you are a Clot, your webhook automatically notifies you when the review is posted. Open the PR to read the completed review for the relevant commit. The status comment can show that the job is queued or running. A clean review explicitly says no issues were found.
- Apply findings that hold up against the code and explain any you decline.

Project-specific details (branch naming, commit style, how many rounds, when to stop, which findings to skip) still come from that project's memory and CLAUDE.md.
