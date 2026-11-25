# Compiling the Reports

Do this only once every reviewer has finished. Read every report in the review directory before deciding anything about any one of them.

## 1. Verify

Reviewers guess at an implementation that doesn't exist yet. For each finding:

- **Check the claim.** If it says the codebase lacks X, look. If it says the plan implies Y, reread the plan. If it cites how a library or production system behaves and the finding hinges on it, confirm it (docs lookup, not memory).
- **Check the likelihood.** Would a competent executor following this plan plausibly ship this? Drop the "every possible mistake at once" scenarios. Keep rare but catastrophic ones.
- **Check it isn't already covered** by the plan, the spec, an existing gotcha, or something in the codebase a reviewer missed.
- **Merge duplicates.** Lenses overlap (bot protection shows up in both scale and security; double-submit in edge cases and UX). Keep one entry, credit both lenses.

Discard what fails. Keep a count per lens for the summary.

## 2. Sort into buckets

**Bucket 1: Apply.** All must hold:
- the finding is true and plausible,
- the fix doesn't contradict any design choice in the spec or plan,
- it doesn't change scope, add a dependency, cost money, or alter user-visible behaviour your human partner might have opinions on.

These should always be done. **Edit the plan now**: add each as a bullet under **Key Decisions & Gotchas** (or into the task's **Build** / **Tests (behaviours)** when it belongs to one task, plus a Manual Verification item when it's user-facing). Write them in the plan's voice: terse, specific, actionable. Merge related ones. Re-check the plan's size budget (writing-plans: roughly 150–400 lines) and tighten if you pushed it over.

**Bucket 2: Your call.** True or probably true, but applying it might go against what your human partner wants: it adds a dependency or a service, grows scope, has a cost, trades UX for safety (CAPTCHA, extra confirmation steps), or contradicts a preference they voiced. Don't apply. Present each with the tradeoff in one or two lines.

**Bucket 3: Design choices to re-evaluate.** Mostly from the design reviewer, plus any reviewer's "Conflicts with a stated decision". For each: the decision as it stands, why it might not be the best choice (the concrete complication it brings, and when), what production systems doing something similar do instead, and what switching would cost now versus later. Don't apply. Your human partner decides.

When a bucket 1 fix depends on a bucket 3 decision (e.g. a gotcha about cache invalidation only matters if the caching design stays), hold it until the decision is made and say so.

## 3. Report to your human partner

Keep it scannable. Use this shape:

```markdown
## Plan review: [plan name]

Ran: design, edge cases, maintainability, scale, security[ (fallback: Opus 4.8)], UI/UX[ / skipped: no GUI]
Kept N of M findings (dropped: X duplicates, Y not plausible, Z already covered)

### Applied to the plan (N)
- [one line each: what was added and where]

### Your call (N)
1. **[title]** ([lens]): [the issue]. Applying it means [tradeoff]. Recommend: [yes/no and why].

### Design choices worth re-evaluating (N)
1. **[decision]** ([lens]): [why it may not be the best choice, and when it bites]. Alternative: [what production systems do]. Cost to switch now vs later: [one line]. Recommend: [keep / switch, and why].

Reports: [review directory path]
```

Then wait. Once your human partner answers buckets 2 and 3, apply what they approved to the plan (including any held bucket 1 items), and offer writing-plans' execution handoff again.
