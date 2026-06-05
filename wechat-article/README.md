# wechat-article

Generate WeChat Official Account (微信公众号) compatible HTML articles with inline styles.

WeChat's editor strips most modern HTML/CSS, so this skill outputs fully inline-styled HTML that can be directly pasted into the editor.

## Usage

```
/wechat-article [markdown-file-or-topic]
```

- Pass a markdown file path to convert it to WeChat-compatible HTML
- Or pass a topic description to generate an article from scratch

## Available Styles

| Style | Trigger | Description |
|-------|---------|-------------|
| **default** | No style specified | 粉色主题，多色辅助，callout 丰富，适合生活化/教程类文章 |
| **qwen** | `qwen` or `千问风格` | 紫色单色系，极简技术感，参考千问大模型公众号排版，适合技术发布/产品介绍类文章 |
| **aliyun** | `aliyun` or `阿里云风格` | 蓝色单色系，企业技术博客风，参考阿里云公众号排版，适合产品发布/开源公告类文章 |
| **aws** | `aws` or `亚马逊风格` | 橙色主调，方框阴影标题，参考亚马逊云开发者公众号，适合技术教程/实战指南类文章 |

### Default (Pink)

- Pink accent (`#ec4899`) with purple secondary (`#a78bfa`)
- Numbered pink badge section headings
- Callout boxes (pink / purple / amber)
- Dark code blocks
- Emoji-friendly, conversational tone

### Qwen (千问)

- Purple monochrome (`#7774e8` / `#615ced` / `#e3e2fa`)
- Centered headings with purple underline block
- Keyword highlight: purple text + light purple background
- No callout boxes, no emoji, minimal decoration
- Formal tech-blog tone

### Aliyun (阿里云)

- Blue monochrome (`#0033cc`)
- H2: 5px blue left border + bold black title
- H3: `# prefix` in blue italic bold
- Images with border-radius, box-shadow, subtle border
- No callout boxes, no emoji, enterprise tech style

### AWS (亚马逊云科技)

- Orange monochrome (`#ff9900`)
- H2: box with 6px orange box-shadow, centered
- H3: orange bar + top line + right/bottom border
- Inline code: red text (`#e60f0b`) + gray background
- Link cards with dashed border, orange bold link names
- Author card with orange square + gradient line
- No callout boxes, no emoji, tutorial-oriented

## Output

- Saves to the same directory as input, or `~/clawd/gzh/<topic-slug>/`
- Filename: `<title>-wx.html`
- Open in browser to preview, then select all inside `<body>` and paste into WeChat editor
