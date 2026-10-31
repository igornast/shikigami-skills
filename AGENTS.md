# Shikigami Skills

This repository publishes free Agent Skills for Shikigami, the desktop app that runs coding agents side by side and connects them through its local MCP server. The skills are the product here. There's no build step and no code to run.

## Layout

The repository is a Claude Code plugin marketplace and the plugin itself at the same time. `.claude-plugin/marketplace.json` lists one plugin, `shikigami`, with `"source": "./"`, and `.claude-plugin/plugin.json` describes it. Claude Code finds every skill under `skills/` without further config. `gemini-extension.json` makes the same folder installable as a Gemini CLI extension, and `npx skills add` reads `skills/` directly.

Each skill lives in `skills/<name>/` with a `SKILL.md` and, when it needs more detail, a `references/` folder one level deep. `SKILL.md` links its references with relative paths, for example `references/shikigami-mcp.md`.

This file is the single source of instructions for every agent working here. `CLAUDE.md` only imports it, so edit this file, never that one.

## Rules for skills

The frontmatter `name` must equal the folder name: lowercase letters, digits and single hyphens, at most 64 characters. Always quote `description` in double quotes. Descriptions tend to contain `: `, which breaks strict YAML parsers such as the one `npx skills` uses, even though Claude Code accepts it. Keep `description` under 1024 characters and start it with "Use when", so agents know when to load it.

Every skill sets `license: MIT` and `metadata.version`. Keep `SKILL.md` under 500 lines and move tables, limits and API detail into `references/`.

Skills are read by agents, often non-native English models, so write them in plain, short English. Describe only what the Shikigami app actually does. Check behaviour against the app repository and its `docs/AGENT-DELEGATION.md` before changing a tool name, setting label, delivery state or limit.

## Versions

The plugin version lives in three places and they must match: `.claude-plugin/plugin.json`, `gemini-extension.json` and the `metadata.version` of each changed skill. Claude Code pins users to the `plugin.json` version, so a change without a version bump never reaches them. Bump the patch for wording fixes and the minor for new skills or changed behaviour.

## Checks

Run these before you call a change done:

```bash
claude plugin validate .
python3 -c "import yaml,glob;[yaml.safe_load(open(p).read().split('---')[1]) for p in glob.glob('skills/*/SKILL.md')];print('frontmatter ok')"
```

The validator warns that the root `CLAUDE.md` is not loaded as part of the plugin. That's expected. It only imports this file for Claude Code, and both files are for working on the repository, not for people who install the plugin.

## Writing public copy

The README, release notes and any other text people read go out under Igor's name. Write them with the `igor-tone` skill if your agent has it. For Claude Code it's linked locally at `.claude/skills/igor-tone`, which points outside this repository and isn't committed. If you don't have the skill, ask before you write copy.

## Git

`.claude/` and `.shiki/` hold local agent and Shikigami state and are ignored. Never commit them, and never commit without being asked.
