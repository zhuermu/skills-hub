# Skills Hub

个人 AI Skills 仓库，收集和管理可复用的 AI agent skills。

## Skills

| Skill | 说明 |
|-------|------|
| [wechat-article](./wechat-article/) | 生成微信公众号兼容的 HTML 文章 |
| [aws-style-presentation](./aws-style-presentation/) | 生成 AWS 风格的 HTML 幻灯片演示 |

## 使用方式

将需要的 skill 目录复制到你的项目中：

```bash
# Kiro
cp -r <skill-name>/ your-project/.kiro/skills/<skill-name>/

# Claude Code
cp -r <skill-name>/ your-project/.claude/skills/<skill-name>/
```

## 贡献新 Skill

每个 skill 一个目录，至少包含 `SKILL.md`。
