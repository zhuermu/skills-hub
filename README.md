# Skills Hub

A curated collection of reusable AI agent skills for [Kiro](https://kiro.dev) and [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

## Available Skills

| Skill | Description |
|-------|-------------|
| [wechat-article](./wechat-article/) | Generate WeChat Official Account (微信公众号) compatible HTML articles with inline styles |
| [aws-style-presentation](./aws-style-presentation/) | Generate AWS-style HTML slide presentations with purple/pink gradient theme |

## Usage

Copy the skill directory into your project:

```bash
# For Kiro
cp -r <skill-name>/ your-project/.kiro/skills/<skill-name>/

# For Claude Code
cp -r <skill-name>/ your-project/.claude/skills/<skill-name>/
```

Or clone this repo as a subdirectory in your workspace and symlink as needed.

## Contributing

Each skill lives in its own directory with at least a `SKILL.md` file. PRs welcome.

## License

MIT
