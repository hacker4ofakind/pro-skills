---
name: verification-before-completion
description: Use when about to claim work is complete, fixed, or passing, before committing or creating PRs - requires running verification commands and confirming output before making any success claims
---

# Verification Before Completion

Evidence before claims. Don't say something passes, works or is fixed unless you ran the command that proves it, in this turn, and read its output.

- **Run the real check, fully.** The full test command, the build, the linter, whichever one the claim is about. Read the output and exit code, don't skim for the word "pass".
- **Match the check to the claim.** A passing linter says nothing about the build. Passing tests say nothing about whether every requirement is met. A changed line says nothing about whether the bug is fixed: reproduce the original symptom.
- **A regression test must be seen failing.** Revert the fix, confirm the test fails, restore the fix, confirm it passes.
- **Delegated work is unverified work.** When an agent or session reports success, check the diff and run the checks yourself before repeating the claim.
- **Requirements: re-read the spec or plan and check each item.** Don't report "done" on the strength of green tests alone.
- **If you didn't verify, or it failed, say so plainly,** with the actual state, instead of hedging with "should work".
