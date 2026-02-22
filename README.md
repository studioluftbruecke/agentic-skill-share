# Agentic Skill Share

A collection of skills for AI coding agents.

Skills are small, self-contained folders that give an agent focused knowledge about a specific API, tool, or workflow.

## Compatible agents

These skills follow the [Agent Skills open standard](https://github.com/anthropics/skills/blob/main/spec/agent-skills-spec.md): Claude Code, GitHub Copilot, Codex CLI, Cursor, Windsurf, Goose, OpenClaw, and growing.

## Skills

| Skill | Description |
|---|---|
| [`coolify-api`](./coolify-api/) | Manage applications, databases, services, and deployments on a [Coolify](https://coolify.io) instance via its REST API |

## How to use

**Clone or copy** the skill folder you want into your project (usually a `.agent/skills/` or `skills/` directory, depending on your agent):

```sh
# example for Claude Code (place skills under .claude/skills/)
cp -r coolify-api /your-project/.claude/skills/
```

This should work normally. When your task matches the skill's description (e.g. "deploy this app with Coolify"), the agent picks it up and applies it.

Some skills require environment variables — check the skill's `SKILL.md` for setup instructions.

## Skill structure

Each skill is a folder with:

```
my-skill/
  SKILL.md          # instructions the agent reads when the skill is active
  _meta.json        # name, description, and trigger phrases
  references/       # detailed docs the agent can look up as needed
  scripts/          # optional runnable helpers the agent can invoke
```

## Contributing

Contributions to existing skills are welcome. If you spot an error, want to improve the docs, or extend a skill’s behavior, open a pull request with your changes.

## License

[Apache 2.0](./LICENSE)
