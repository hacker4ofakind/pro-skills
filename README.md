# pro-skills

A small set of skills for Claude Code and Codex that take a big change from idea to verified result: brainstorm, spec, plan, stress-test the plan, execute, verify.

## Skills

| Skill | Use it when |
| --- | --- |
| `brainstorming` | Before creative work: new features, components, or behavior changes. Explores intent and design before any code. Skipped for small fixes when you say to work directly on master/main. |
| `writing-plans` | You have a spec for a multi-step task. Produces a decision-focused plan with a manual verification checklist. |
| `stress-testing-plans` | After a plan is saved. Independent reviewers check the spec and plan for design, edge cases, maintainability, scale, security, and UI/UX (only when there is a GUI). |
| `executing-plans` | You have a written plan to execute in a separate session with review checkpoints. |
| `executor-verifier-workflow` | Big changes that go brainstorming, spec, plan, execute. Covers starting the executor session and a clean-context verifier session. |
| `verification-before-completion` | Before claiming work is done, fixed, or passing. Requires running the verification commands first. |

The reviewers used by `stress-testing-plans` live in [`agents/`](agents).

## Install

### Claude Code

Add this repo as a marketplace, then install the plugin:

```
/plugin marketplace add hacker4ofakind/pro-skills
/plugin install pro-skills@pro-skills-dev
```

For local development, point the marketplace at your checkout instead:

```
/plugin marketplace add /path/to/pro-skills
```

### Codex

The plugin manifest is in [`.codex-plugin/plugin.json`](.codex-plugin/plugin.json) and the marketplace entry is in [`.agents/plugins/marketplace.json`](.agents/plugins/marketplace.json). Add this repo as a Codex plugin marketplace and install `pro-skills`.

## Where files go

Skills write specs and plans into the project you're working in, under `.pro-skills/specs/` and `.pro-skills/plans/`.

## License

MIT, see [LICENSE](LICENSE).
