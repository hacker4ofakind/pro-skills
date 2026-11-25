---
name: brainstorming
description: "Recommended before creative work - creating features, building components, adding functionality, or modifying behavior. Explores user intent, requirements and design before implementation. Skip for fixes/small changes when told to work directly on master/main: no spec or plan."
---

# Brainstorming Ideas Into Designs

## Small Changes: No Spec or Plan

When your human partner tells you to work directly on master/main (whichever the repo uses) for a fix or small change, do not write a spec or a plan, and do not update existing ones. Specs and plans are only for new features, or when they explicitly invoke this skill. The same applies to a change that is plainly a fix or tweak. It overrides the habit of keeping `.pro-skills/` specs and plans in step with the build for such changes. A new feature, a stage, or an explicit brainstorming request still gets the full pipeline.

**Why:** For small changes the spec/plan overhead is wasted; the code and git history are the record (stated 2026-11-22, after a small change to how Clots learn about each other).

Help turn ideas into fully formed designs and specs through natural collaborative dialogue.

Start by understanding the current project context, then ask questions one at a time to refine the idea. Once you understand what you're building, present the design and get user approval before writing any code.

## Fast Path: Well-Diagnosed, Localized Bugs

Skip brainstorming (and the rest of the spec → plan → execute pipeline) entirely when your human partner hands you a bug report that already contains the diagnosis — what happens, when it happens, when it doesn't, and their suspected cause — and the fix is confined to one part of the codebase rather than rippling across many subsystems. Just fix it. Brainstorming exists to surface unknowns; if your human partner already did that work and the blast radius is small, the full pipeline adds delay without adding insight.

This does NOT apply to: new features, bugs whose cause is unclear or unconfirmed, bugs that touch multiple areas of the app, or reports that describe only symptoms without a suspected cause. When in doubt, treat it as needing brainstorming.

## Checklist

Create a task for each of these items and complete them in order:

1. **Explore project context** — check files, docs, recent commits
2. **Check the bug fast path** — if this is a well-diagnosed, localized bug (see above), skip the rest of this checklist and just fix it
3. **Ask clarifying questions** — one at a time, understand purpose/constraints/success criteria
4. **Propose approaches, only if genuinely needed** — if there are meaningfully different paths with different trade-offs, propose 2-3 with your recommendation. Skip this when your human partner has already specified how they want it built, or when there's really only one reasonable way to do it.
5. **Present design** — as a single message covering all sections at once; only walk through it section-by-section, pausing after each, if your human partner explicitly asks for that
6. **Write design doc** — save to `.pro-skills/specs/YYYY-MM-DD-<topic>-design.md` and commit
7. **Spec self-review** — quick inline check for placeholders, contradictions, ambiguity, scope (see below)
8. **Transition to implementation** — once self-review passes, invoke writing-plans directly to create the implementation plan

**The terminal state is invoking writing-plans.** Do NOT invoke frontend-design, mcp-builder, or any other implementation skill. The ONLY skill you invoke after brainstorming is writing-plans.

## The Process

**Understanding the idea:**

- Check out the current project state first (files, docs, recent commits)
- Before asking detailed questions, assess scope: if the request describes multiple independent subsystems (e.g., "build a platform with chat, file storage, billing, and analytics"), flag this immediately. Don't spend questions refining details of a project that needs to be decomposed first.
- If the project is too large for a single spec, help the user decompose into sub-projects: what are the independent pieces, how do they relate, what order should they be built? Then brainstorm the first sub-project through the normal design flow. Each sub-project gets its own spec → plan → implementation cycle.
- For appropriately-scoped projects, ask questions one at a time to refine the idea
- Only one question per message - if a topic needs more exploration, break it into multiple questions
- Focus on understanding: purpose, constraints, success criteria

**Exploring approaches:**

- Only propose multiple approaches when they'd genuinely lead to different outcomes — different architectures, different trade-offs, different user experiences. If your human partner already told you how they want it built, or there's really only one reasonable way to do it, skip straight to that approach. Presenting fake alternatives just to check a box wastes their time.
- When there are real alternatives, propose 2-3 with trade-offs, present them conversationally, lead with your recommendation, and explain why
- YAGNI ruthlessly - remove unnecessary features from every approach and design

**Presenting the design:**

- Once you believe you understand what you're building, present the whole design in a single message — all sections at once, not doled out one at a time
- Scale each section to its complexity: a few sentences if straightforward, up to 200-300 words if nuanced
- Cover: architecture, components, data flow, error handling, testing
- Ask for approval once, at the end, covering the whole design
- Only walk through it section-by-section, pausing for approval after each, if your human partner explicitly asks for that instead
- Be ready to go back and clarify if something doesn't make sense

## After the Design

**Documentation:**

- Write the validated design (spec) to `.pro-skills/specs/YYYY-MM-DD-<topic>-design.md`
  - (User preferences for spec location override this default)
- Use elements-of-style:writing-clearly-and-concisely skill if available
- Commit the design document to git

**Spec Self-Review:**
After writing the spec document, look at it with fresh eyes:

1. **Placeholder scan:** Any "TBD", "TODO", incomplete sections, or vague requirements? Fix them.
2. **Internal consistency:** Do any sections contradict each other? Does the architecture match the feature descriptions?
3. **Scope check:** Is this focused enough for a single implementation plan, or does it need decomposition?
4. **Ambiguity check:** Could any requirement be interpreted two different ways? If so, pick one and make it explicit.

Fix any issues inline. No need to re-review — just fix and move on.

**Implementation:**

- Your human partner already approved the design content in the previous step — there's no need to ask them to separately review the file you just wrote it to. Once self-review passes, go straight to invoking the writing-plans skill to create the implementation plan.
- Do NOT invoke any other skill. writing-plans is the next step.

## Visual Companion

Whenever something would be hard to explain in words (a website redesign, a mobile design, an app designed from scratch), prefer showing it. Either write standalone HTML files and serve them with `pnpm dlx serve`, or use something like Claude Design. Pick whichever suits the situation.
