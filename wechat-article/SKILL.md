---
name: wechat-article
description: Generate WeChat public account (微信公众号) compatible HTML articles. Use when user wants to write a WeChat article, create gzh content, or convert markdown to WeChat-compatible HTML.
argument-hint: "[markdown-file-or-topic]"
disable-model-invocation: true
allowed-tools: Bash, Read, Glob, Write
---

# Generate WeChat 公众号 Compatible HTML Article

Convert content (markdown or topic description) into a self-contained HTML file that can be directly pasted into the WeChat public account editor.

## WeChat HTML Compatibility Rules

WeChat's editor strips most modern HTML/CSS. Follow these rules strictly:

### MUST DO
- **All styles inline** — every tag needs a `style=""` attribute. No `<style>` blocks, no CSS classes.
- **Use `<section>` instead of `<div>`** — WeChat strips `<div>` in some contexts.
- **Images as base64 `data:` URIs or WeChat CDN URLs** — external image URLs get blocked.
- **Font stack**: `-apple-system,BlinkMacSystemFont,"PingFang SC","Microsoft YaHei","Segoe UI",Roboto,sans-serif`
- **Max width 680px** on body (WeChat article viewport)
- **`line-height: 1.8`** for readability on mobile

### MUST NOT
- No `<script>` tags
- No external CSS (`<link>` or `<style>`)
- No CSS classes or IDs for styling
- No `position: fixed/sticky`
- No `display: flex/grid` (use tables for layout)
- No `@media` queries
- No `<iframe>`, `<video>`, `<audio>`
- No `<div>` (prefer `<section>`)
- No SVG (use images instead)

## Design System (微信公众号风格)

### Color Palette
```
Primary Green:  #07c160  (WeChat brand, for accents/borders)
Text Primary:   #1a1a1a
Text Body:      #333
Text Secondary: #555
Text Muted:     #888 / #999
Background:     #fff
Light BG:       #f7f7f7
Border:         #e5e5e5
```

### Callout Box Styles

**Info (blue)**:
```html
<section style="background:#f0f9ff;border-left:4px solid #3b82f6;padding:15px 20px;margin:20px 0;border-radius:0 8px 8px 0;font-size:14px;">
  content here
</section>
```

**Warning (amber)**:
```html
<section style="background:#fff7ed;border-left:4px solid #f59e0b;padding:15px 20px;margin:20px 0;border-radius:0 8px 8px 0;font-size:14px;">
  content here
</section>
```

**Success (green)**:
```html
<section style="background:#f0fdf4;border-left:4px solid #07c160;padding:15px 20px;margin:20px 0;border-radius:0 8px 8px 0;font-size:14px;">
  content here
</section>
```

**Lead/Abstract (gray)**:
```html
<section style="font-size:15px;color:#555;background:#f7f7f7;padding:15px 20px;border-radius:8px;margin:20px 0;line-height:1.9;">
  content here
</section>
```

**Dark highlight (for key insights/quotes)**:
```html
<section style="background:#1a1a2e;color:#e0e0e0;padding:25px;border-radius:12px;margin:20px 0;line-height:1.9;font-size:15px;">
  <strong style="color:#ffd700;">Title</strong><br><br>
  Body text. <strong style="color:#4fc3f7;">Accent text</strong>.
</section>
```

**Purple highlight**:
```html
<section style="background:#4a148c;color:#fff;padding:20px 25px;border-radius:12px;margin:20px 0;line-height:1.8;">
  <strong style="color:#ffd700;">Title</strong><br><br>
  Body text.
</section>
```

### Heading Styles

**H1 (title)**:
```html
<h1 style="font-size:22px;font-weight:bold;color:#1a1a1a;text-align:center;margin:30px 0 10px;line-height:1.4;">Title<br>Subtitle</h1>
<p style="text-align:center;color:#888;font-size:14px;margin-bottom:30px;">tagline</p>
```

**H2 (section heading with numbered green badge)**:
```html
<h2 style="font-size:18px;font-weight:bold;color:#1a1a1a;margin:35px 0 15px;padding-left:12px;border-left:4px solid #07c160;">
  <span style="display:inline-block;background:#07c160;color:#fff;width:24px;height:24px;line-height:24px;text-align:center;border-radius:50%;font-size:13px;font-weight:bold;margin-right:8px;">1</span>
  Section Title
</h2>
```

**H3 (subsection)**:
```html
<h3 style="font-size:16px;font-weight:bold;color:#333;margin:25px 0 10px;">Subsection</h3>
```

### Table Style
```html
<table style="width:100%;border-collapse:collapse;margin:15px 0;font-size:14px;">
  <tr>
    <th style="background:#f3f4f6;padding:10px 12px;text-align:left;font-weight:bold;border:1px solid #e5e5e5;">Header</th>
  </tr>
  <tr>
    <td style="padding:10px 12px;border:1px solid #e5e5e5;">Cell</td>
  </tr>
</table>
```

### Code Block (dark theme section, NOT `<pre><code>`)
```html
<section style="background:#1e1e1e;color:#d4d4d4;padding:16px 20px;border-radius:8px;font-size:13px;line-height:1.7;margin:15px 0;overflow-x:auto;">
  <span style="color:#569cd6;">keyword</span>
  <span style="color:#dcdcaa;">function</span>
  <span style="color:#ce9178;">"string"</span>
  <span style="color:#6a9955;">// comment</span>
</section>
```

### Inline Elements
- **Bold**: `<strong style="color:#1a1a1a;">text</strong>`
- **Tag/Badge**: `<span style="background:#e8f5e9;color:#2e7d32;padding:2px 6px;border-radius:4px;font-size:12px;">tag</span>`
- **Red badge**: `<span style="background:#fce4ec;color:#c62828;padding:2px 6px;border-radius:4px;font-size:12px;">warning</span>`

### Footer
```html
<section style="margin-top:30px;padding:20px 25px;background:#f7f7f7;border-radius:8px;text-align:center;line-height:1.9;">
  <p style="font-size:15px;color:#333;margin:0 0 8px;">如果这篇文章对你有帮助，欢迎<strong style="color:#07c160;">点赞、在看、转发</strong>三连。</p>
  <p style="font-size:14px;color:#666;margin:0;">关注我，持续分享 AI 时代的技术实战与深度思考。</p>
</section>
```

## Steps

1. **Determine input**: If `$ARGUMENTS` is a file path, read it. If it's a topic description, use it as the article theme.

2. **If markdown file**: Convert to WeChat-compatible HTML following all rules above. Process:
   - `# H1` → centered title
   - `## H2` → numbered section headings with green badges
   - `### H3` → subsection headings
   - `> blockquote` → callout boxes (choose color by context)
   - `**bold**` → `<strong>` with inline style
   - Tables → inline-styled tables
   - Code blocks → dark-theme sections with syntax coloring
   - Images → base64 encode if local file exists
   - Lists → styled `<p>` with bullet characters or `<section>` blocks

3. **If topic description**: Write the article content first, then convert to HTML.

4. **Wrap in full HTML document**:
   ```html
   <!DOCTYPE html>
   <html lang="zh-CN">
   <head>
   <meta charset="UTF-8">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <title>Article Title</title>
   </head>
   <body style="font-family:-apple-system,BlinkMacSystemFont,'PingFang SC','Microsoft YaHei','Segoe UI',Roboto,sans-serif;line-height:1.8;color:#333;max-width:680px;margin:0 auto;padding:20px;background:#fff;">
   ... content ...
   </body>
   </html>
   ```

5. **Save output**: Write to the same directory as input, or to `~/clawd/gzh/<topic-slug>/` if creating from scratch. Filename: `<title>-wx.html`

6. **Report**: Show file path, size, and remind user to:
   - Open in browser to preview
   - Copy the `<body>` inner HTML into WeChat editor
   - Check image rendering (base64 images may need re-upload via WeChat)

## Writing Style

- 朋友聊天式，不要机构感
- 技术内容要准确但不枯燥
- 适当使用 emoji 但不过度
- 段落不要太长，移动端阅读体验优先
- 每个 section 之间留足视觉间距
