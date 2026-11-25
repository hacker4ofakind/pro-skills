# pro-skills

Skills and reviewer agents for Claude Code and Codex. No other harnesses are supported, so don't add manifests or rule files for them.

## Layout

- `skills/<name>/SKILL.md`: one skill per directory.
- `agents/`: reviewer agents dispatched by `stress-testing-plans`.
- `.claude-plugin/`: Claude Code plugin and marketplace manifests.
- `.codex-plugin/` and `.agents/plugins/`: Codex plugin manifest and marketplace entry.

## Conventions

- Skills are code that shapes agent behavior. Change them deliberately and keep the wording tight.
- Cross-references between skills use the `pro-skills:<skill-name>` form.
- Specs and plans written by the skills go in `.pro-skills/specs/` and `.pro-skills/plans/` of the target project.
- Keep the plugin zero-dependency.
- When the version changes, update it in `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`, and `.codex-plugin/plugin.json` together.
