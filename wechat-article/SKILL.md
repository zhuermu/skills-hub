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
- **Font stack**: `"PingFang SC","Hiragino Sans GB","Microsoft YaHei","WenQuanYi Micro Hei","Helvetica Neue",Arial,sans-serif`
- **Max width 680px** on body (WeChat article viewport)
- **All text elements need `letter-spacing: 1px`** for Chinese readability

### MUST NOT
- No `<script>` tags
- No external CSS (`<link>` or `<style>`)
- No CSS classes or IDs for styling
- No `position: fixed/sticky`
- No `display: flex/grid` (use tables for layout if needed)
- No `@media` queries
- No `<iframe>`, `<video>`, `<audio>`
- No `<div>` (prefer `<section>`)
- No SVG (use images instead)
- No `white-space: pre-wrap` for code blocks (WeChat strips it)
- No `<pre><code>` (WeChat renders them poorly)

## Typography System (排版参数)

These parameters are based on professional WeChat article typography best practices:

### Body Text
```
font-size:       15px
color:           #3f3f3f  (not pure black — softer on eyes)
line-height:     2.0      (generous for mobile reading)
letter-spacing:  1px      (standard for Chinese text)
margin:          20px 0   (paragraph spacing)
padding:         0 8px    (slight side indent for breathing room)
```

### Key Typography Rules
1. **Never use pure black (#000)** — use #3f3f3f for body, #2b2b2b for headings
2. **Line height 2.0** — the single most important readability factor on mobile
3. **Letter spacing 1px** — standard for Chinese, makes text less cramped
4. **Paragraph spacing 20px** — clear visual separation between paragraphs
5. **Side padding 8px** on text — gives content breathing room from edges
6. **Image full-bleed** — images should be 100% width with no side padding
7. **Max 35 characters per line** — natural at 15px + 680px width
8. **Bold text use #2b2b2b** — slightly darker than body for emphasis

## Color Palette (粉色主题，多色辅助，层次分明)

```
Heading Text:    #2b2b2b
Body Text:       #3f3f3f
Secondary Text:  #888888
Muted Text:      #aaaaaa
Background:      #ffffff

Primary Pink:    #ec4899  (badges, links, borders, CTA)
Secondary Purple:#a78bfa  (辅助色, 次要边框)
Amber Accent:    #f59e0b  (warning 边框)
Light Pink BG:   #fdf2f8  (primary callout, footer)
Light Purple BG: #f5f3ff  (secondary callout)
Amber Warning BG:#fffbeb  (warning callout — 暖黄，跟粉色系明显区分)
Code Block BG:   #1e293b  (深蓝灰，跟正文强对比)
Code Text:       #e2e8f0  (代码浅色字)
Code Comment:    #94a3b8  (代码注释色)
Code Border:     #334155  (代码块边框)
Table Header BG: #fce7f3  (表格头，比 callout 深一级)
Table Border:    #f9a8d4  (表格边框)
Border Pink:     #f9a8d4  (section borders)
Border Purple:   #c4b5fd  (辅助边框)
Badge Pink BG:   #fce7f3
Badge Purple BG: #ede9fe
```

## Component Library

### H1 (Article Title)
```html
<h1 style="font-size:22px;font-weight:bold;color:#2b2b2b;text-align:center;margin:30px 0 6px;line-height:1.5;letter-spacing:1px;">
  Title Line 1<br>Title Line 2
</h1>
<p style="text-align:center;color:#aaa;font-size:13px;margin-bottom:35px;letter-spacing:1px;">tagline · keywords</p>
```

### H2 (Section Heading — numbered pink badge)
```html
<h2 style="font-size:17px;font-weight:bold;color:#2b2b2b;margin:40px 0 18px;padding-left:12px;border-left:3px solid #f9a8d4;letter-spacing:1px;">
  <span style="display:inline-block;background:#ec4899;color:#fff;width:22px;height:22px;line-height:22px;text-align:center;border-radius:50%;font-size:12px;font-weight:bold;margin-right:8px;">1</span>
  Section Title
</h2>
```

### H3 (Subsection)
```html
<h3 style="font-size:15px;font-weight:bold;color:#2b2b2b;margin:28px 0 12px;padding:0 8px;letter-spacing:1px;">Subsection Title</h3>
```

### Body Paragraph
```html
<p style="font-size:15px;color:#3f3f3f;margin:20px 0;line-height:2.0;letter-spacing:1px;padding:0 8px;">
  Paragraph text here.
</p>
```

### Bullet List Item
```html
<p style="font-size:15px;color:#3f3f3f;margin:10px 0 10px 8px;line-height:2.0;letter-spacing:1px;padding:0 8px;">
  • <strong style="color:#2b2b2b;">Label</strong>：Description text
</p>
```

### Callout Boxes

**Pink info callout (primary)**:
```html
<section style="background:#fdf2f8;border-left:3px solid #ec4899;padding:16px 20px;margin:22px 8px;border-radius:0 8px 8px 0;font-size:14px;color:#3f3f3f;line-height:1.9;letter-spacing:0.5px;">
  content here
</section>
```

**Purple secondary callout**:
```html
<section style="background:#f5f3ff;border-left:3px solid #a78bfa;padding:16px 20px;margin:22px 8px;border-radius:0 8px 8px 0;font-size:14px;color:#3f3f3f;line-height:1.9;letter-spacing:0.5px;">
  content here
</section>
```

**Rose warning callout**:
```html
<section style="background:#fffbeb;border-left:3px solid #f59e0b;padding:16px 20px;margin:22px 8px;border-radius:0 8px 8px 0;font-size:14px;color:#78350f;line-height:1.9;letter-spacing:0.5px;">
  content here
</section>
```

**Soft pink highlight block (for key insights — primary)**:
```html
<section style="background:#fdf2f8;color:#831843;padding:22px 24px;border-radius:10px;margin:22px 0;line-height:2.0;font-size:15px;letter-spacing:1px;">
  <strong style="color:#be185d;">✦ Title</strong><br><br>
  Body text here.
</section>
```

**Soft purple highlight block (secondary)**:
```html
<section style="background:#f5f3ff;color:#4a1a6b;padding:22px 24px;border-radius:10px;margin:22px 0;line-height:2.0;font-size:15px;letter-spacing:1px;">
  <strong style="color:#7c3aed;">✦ Title</strong><br><br>
  Body text here.
</section>
```

**Lead/Abstract**:
```html
<section style="font-size:15px;color:#666;background:#fef1f6;padding:18px 22px;border-radius:10px;margin:22px 0;line-height:2.0;letter-spacing:1px;">
  Abstract or summary text.
</section>
```

### Code Block (WeChat-safe — NO pre/code tags, NO white-space:pre)

Use `<section>` with `<br>` for line breaks and `&nbsp;` for indentation.
Use dark background for code blocks — strong contrast with body text makes code instantly recognizable. WeChat preserves dark backgrounds reliably.

```html
<section style="background:#1e293b;border:1px solid #334155;padding:16px 20px;border-radius:8px;font-size:13px;line-height:1.8;margin:18px 8px;color:#e2e8f0;">
  for pct in range(5, 96, 5):<br>
  &nbsp;&nbsp;&nbsp;&nbsp;if len(good_frames) &gt;= 5:<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;break<br>
  &nbsp;&nbsp;&nbsp;&nbsp;# comment here<br>
  &nbsp;&nbsp;&nbsp;&nbsp;img = cv2.imread(str(frame_path))
</section>
```

**Rules for code blocks:**
- Use `<br>` for every line break
- Use `&nbsp;` (×4 per indent level) for indentation
- Escape `<` as `&lt;`, `>` as `&gt;`
- Do NOT use `<span>` for syntax highlighting — it breaks when combined with `&nbsp;`/`<br>`
- Keep code blocks short (≤15 lines). For longer code, link to GitHub.
- Dark background (#1e293b) with light text (#e2e8f0) — creates strong visual separation from body text
- If dark background gets stripped in WeChat editor, fall back to `background:#f1f5f9;color:#334155;border:1px solid #e2e8f0` (light slate gray)

### Table
```html
<table style="width:100%;border-collapse:collapse;margin:18px 0;font-size:13px;letter-spacing:0.5px;">
  <tr>
    <th style="background:#fce7f3;padding:10px 12px;text-align:left;font-weight:bold;border:1px solid #f9a8d4;color:#2b2b2b;">Header</th>
  </tr>
  <tr>
    <td style="padding:10px 12px;border:1px solid #f9a8d4;color:#3f3f3f;">Cell</td>
  </tr>
</table>
```

### Image
```html
<img src="data:image/png;base64,..." alt="description" style="width:100%;border-radius:8px;margin:18px 0;">
```
- Always 100% width (full bleed)
- border-radius for softness
- No side padding (images go edge to edge)

### Inline Elements
- **Bold**: `<strong style="color:#2b2b2b;">text</strong>`
- **Link**: `<a href="url" style="color:#ec4899;text-decoration:none;">text</a>`
- **Purple badge**: `<span style="background:#ede9fe;color:#6d28d9;padding:2px 8px;border-radius:4px;font-size:12px;">tag</span>`
- **Pink badge**: `<span style="background:#fce7f3;color:#be185d;padding:2px 8px;border-radius:4px;font-size:12px;">tag</span>`

### Separator
```html
<section style="text-align:center;margin:35px 0;color:#d4d4d4;letter-spacing:6px;font-size:12px;">• • •</section>
```

### Footer
```html
<section style="margin-top:35px;padding:22px 25px;background:#fdf2f8;border-radius:10px;text-align:center;line-height:2.0;">
  <p style="font-size:15px;color:#3f3f3f;margin:0 0 8px;letter-spacing:1px;">如果这篇文章对你有帮助，欢迎<strong style="color:#ec4899;">点赞、在看、转发</strong>三连 🙏</p>
  <p style="font-size:13px;color:#888;margin:0;letter-spacing:1px;">关注我，持续分享 AI 时代的技术实战与深度思考</p>
</section>
```

## Document Template

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Article Title</title>
</head>
<body style="font-family:'PingFang SC','Hiragino Sans GB','Microsoft YaHei','WenQuanYi Micro Hei','Helvetica Neue',Arial,sans-serif;line-height:2.0;color:#3f3f3f;max-width:680px;margin:0 auto;padding:20px 0;background:#fff;">
... content ...
</body>
</html>
```

## Steps

1. **Determine input**: If `$ARGUMENTS` is a file path, read it. If it's a topic description, use it as the article theme.

2. **If markdown file**: Convert to WeChat-compatible HTML following all rules above. Process:
   - `# H1` → centered title with tagline
   - `## H2` → numbered section headings with pink badges
   - `### H3` → subsection headings
   - `> blockquote` → callout boxes (choose color by context)
   - `**bold**` → `<strong>` with `color:#2b2b2b`
   - Tables → inline-styled tables with pink borders
   - Code blocks → light pink `<section>` with `<br>` line breaks and `&nbsp;` indentation (NO `<pre>`, NO `white-space:pre`, NO syntax highlighting spans)
   - Images → base64 encode if local file exists, full-width
   - Lists → styled `<p>` with bullet characters
   - `---` → dot separator

3. **If topic description**: Write the article content first, then convert to HTML.

4. **Wrap in full HTML document** using the template above.

5. **Save output**: Write to the same directory as input, or to `~/clawd/gzh/<topic-slug>/` if creating from scratch. Filename: `<title>-wx.html`

6. **Report**: Show file path, size, and remind user to:
   - Open in browser to preview
   - Select all inside `<body>` and copy into WeChat editor
   - Base64 images may need re-upload via WeChat image tool if they don't render

## Writing Style

- 朋友聊天式，不要机构感，不要 AI 味
- 技术内容要准确但不枯燥
- 适当使用 emoji 但不过度（每个 section 最多 1-2 个）
- 段落不要太长——每段 3-5 行为宜，移动端阅读体验优先
- 每个 section 之间留足视觉间距（用 separator 或 margin）
- 用短句，少用长定语从句
- 口语化表达优先："说白了"、"你想"、"等等" > "本质上"、"从角度看"、"值得注意的是"
