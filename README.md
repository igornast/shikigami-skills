# Shikigami Skills

Agent Skills for [Shikigami](https://shikigami.dev), a free desktop app that runs coding agents side by side. Claude Code, Codex, Vibe and opencode each get their own terminal, and a local MCP server inside the app lets them hand work to each other and report back.

That MCP server gives agents the tools to talk. It doesn't teach them how to run a team. These skills do that part.

## What's in here

There's one skill so far, `shikigami-supervisor`. It turns an agent into a supervisor that works through a plan one task at a time. It doesn't write the feature code itself.

For every task it starts a fresh developer, then a fresh reviewer, and removes both once the task passes. A new agent only knows the plan and what the supervisor sends it, so mistakes from task two don't leak into task five. `qa` and `scout` agents join only when the plan gives them real work. Developers run on a cheaper model like `sonnet` at medium effort, QA on the same model at high effort, and the reviewer on a stronger one like `opus` at medium effort. The supervisor also checks every task itself and doesn't trust a report that says the tests pass.

It also knows the parts of the MCP server that are easy to get wrong. `DELIVERED` only means the text landed in a terminal, a `PENDING_DELIVERY` message is never sent later, and two agents writing the same files in one worktree will corrupt each other's work.

The skill reads [`references/shikigami-mcp.md`](skills/shikigami-supervisor/references/shikigami-mcp.md) before its first call. That file covers the tool arguments, delivery states and limits. Read it yourself if you want to know exactly what the agents can and can't do.

## Before you start

The skill only works inside Shikigami, with delegation turned on. Open Settings, go to Agent Delegation and switch on "Enable agent delegation". Restart any agent that was already running, because agents only get the MCP server when they start.

For a fresh agent per task, also switch on "Let agents start and remove agents". It's off by default. Without it, the supervisor falls back to a developer you started yourself and reuses it for every task. Each started agent asks for permissions like any other, so expect a few prompts per task.

Claude Code, Codex and Vibe can all act as supervisor or worker. opencode can receive messages but can't report back, so don't use it as a worker you need an answer from.

## Install

For Claude Code, add this repository as a plugin marketplace and install the plugin:

```text
/plugin marketplace add igornast/shikigami-skills
/plugin install shikigami@shikigami-skills
```

Third-party marketplaces don't auto-update by default, so run `/plugin marketplace update shikigami-skills` to pick up new versions.

For Codex, Gemini CLI, Cursor and other agents that read the [Agent Skills](https://agentskills.io) format, the [`skills` CLI](https://github.com/vercel-labs/skills) installs it for whichever agents it finds:

```bash
npx skills add igornast/shikigami-skills
```

Gemini CLI can also install the repository as an extension:

```bash
gemini extensions install https://github.com/igornast/shikigami-skills
```

If you'd rather not use any tooling, copy the folder into your agent's skills directory, for example `~/.claude/skills/` for Claude Code or `~/.agents/skills/` for Codex:

```bash
cp -r skills/shikigami-supervisor ~/.claude/skills/
```

## Usage

Start one agent in your workspace and ask it to supervise:

```text
Supervise the implementation of docs/plans/search-filters.md with the developer agent.
```

The skill triggers on requests like that. In Claude Code you can also call it directly with `/shikigami:shikigami-supervisor`. The supervisor registers, reads the plan, tells you the tasks and the team it will use, and starts the first task. It stops to ask you only for decisions nobody made up front, blockers only you can clear, or the same problem failing review twice.

With auto-submit off, which is the default, every delegated message waits in the recipient's prompt until you press Enter. That's slower, but you see everything the agents send each other. I'd keep it that way until you trust the setup.

## License

The skills are MIT licensed, so you can use, change and share them freely. See [LICENSE](LICENSE). Shikigami itself is free to use but has its own license.

If the skills are useful to you, a star on GitHub helps other people find them.
