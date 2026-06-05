# Skills Hub

A curated collection of reusable AI agent skills.

Each skill follows the open [Agent Skills](https://agentskills.io/) standard — a `SKILL.md` file (with YAML frontmatter) inside its own folder. Because the format is an open standard, these skills are **portable**: drop them into any agent that supports skills and they work, no rewrites needed.

## Available Skills

| Skill | Description |
|-------|-------------|
| [wechat-article](./wechat-article/) | Generate WeChat Official Account (微信公众号) compatible HTML articles with inline styles |
| [aws-style-presentation](./aws-style-presentation/) | Generate AWS-style HTML slide presentations with purple/pink gradient theme |

## Compatible Agents

Any agent that reads the `SKILL.md` standard can use these skills. Mainstream agents include:

| Agent | User-level skills directory |
|-------|------------------------------|
| [Kiro](https://kiro.dev) | `~/.kiro/skills/<skill>/` |
| [Claude Code](https://docs.anthropic.com/en/docs/claude-code) | `~/.claude/skills/<skill>/` |
| [OpenAI Codex](https://developers.openai.com/codex/skills) | `~/.codex/skills/<skill>/` |
| [Cursor](https://cursor.com) | `~/.cursor/skills/<skill>/` |
| [OpenCode](https://opencode.ai/docs/skills/) | `~/.config/opencode/skills/<skill>/` |
| [GitHub Copilot](https://docs.github.com/copilot) | per Copilot skills docs |
| [Gemini CLI](https://github.com/google-gemini/gemini-cli) | per Gemini CLI skills docs |
| Aider, Windsurf, Zed, Factory, Jules, … | per each tool's skills docs |

> Tip: most tools also support **project-level** skills checked into the repo. The cross-platform convention is `.agents/skills/<skill>/`, while some agents use their own folder (e.g. `.kiro/skills/`, `.claude/skills/`). Check your agent's docs for the exact path.

## Usage

Copy a skill into your agent's skills directory. Examples:

```bash
# Kiro (project-level)
cp -r <skill-name>/ your-project/.kiro/skills/<skill-name>/

# Claude Code (project-level)
cp -r <skill-name>/ your-project/.claude/skills/<skill-name>/

# Codex (user-level, available across all projects)
cp -r <skill-name>/ ~/.codex/skills/<skill-name>/

# Cross-platform convention (project-level, recognized by most tools)
cp -r <skill-name>/ your-project/.agents/skills/<skill-name>/
```

Or clone this repo into your workspace and symlink the skills you need.

## Contributing

Each skill lives in its own directory with at least a `SKILL.md` file. Keep the `SKILL.md` frontmatter (`name`, `description`) accurate so agents can auto-discover and trigger it. PRs welcome.

## License

MIT
