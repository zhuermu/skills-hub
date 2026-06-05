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
- **Text elements should set `letter-spacing`** for Chinese readability — `1px` is the default/pink and aliyun/aws baseline; qwen omits it and relies on `line-height:2.2` for breathing room instead. Follow the selected style's spec.

### MUST NOT
- No `<script>` tags
- No external CSS (`<link>` or `<style>`)
- No CSS classes or IDs for styling
- No `position: fixed/sticky`
- No CSS `grid` (`display: grid`) — use simple `flex` rows or tables instead
- No `@media` queries
- No `<iframe>`, `<video>`, `<audio>`
- No `<div>` (prefer `<section>`)
- No SVG (use images instead)
- No `white-space: pre-wrap` for code blocks (WeChat strips it)
- No `<pre><code>` (WeChat renders them poorly)

### Use with care
- **`display: flex`** — WeChat *does* preserve simple single-row flex layouts (`display:flex;flex-flow:row;`), and they are widely used by mainstream 公众号 排版工具 (135editor, 壹伴) for side-by-side headings, author cards, and CTA blocks. Several styles below (qwen/aliyun/aws) rely on it. Constraints: keep to **one-level, single-row** flex; avoid nested flex containers, `flex-wrap`, column flex, and `gap`. The **default (pink) style stays flex-free** — use only `<section>`/`<table>` there for maximum compatibility.

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

## Style: qwen (千问风格)

参考千问大模型公众号的排版风格。整体偏简洁技术感，以紫色为主色调，没有花哨装饰，强调内容本身。

### Qwen Color Palette

```
Primary Purple:     #7774e8  (rgb(119,116,232) — 标题、链接、强调)
Deep Purple:        #615ced  (rgb(97,92,237) — CTA、footer 强调)
Light Purple BG:    #e3e2fa  (rgb(227,226,250) — 标题底部色块、代码高亮背景)
Body Text:          #3e3e3e  (rgb(62,62,62) — 正文)
Heading Text:       #3e3e3e  (同正文)
Background:         #ffffff
```

### Qwen Typography

```
font-family:   PingFangSC-light, "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", sans-serif
font-size:     15px
line-height:   2.2
text-align:    justify
color:         #3e3e3e
```

### Qwen Components

#### H1 (Article Title — 默认微信标题样式，不做特殊处理)

千问风格标题直接使用微信编辑器自带的 h1，不内联特殊样式。

#### H2 (Section Heading — 居中紫色文字 + 底部浅紫色块)

```html
<section style="margin-top:10px;margin-bottom:10px;text-align:center;">
  <section style="display:inline-block;vertical-align:top;">
    <section style="margin-bottom:-6px;line-height:1;padding-left:2px;padding-right:2px;color:#7774e8;font-size:20px;">
      <p style="margin:0;padding:0;"><b>章节标题</b></p>
    </section>
    <section style="width:100%;height:10px;background-color:#e3e2fa;"></section>
  </section>
</section>
```

#### H3 (Sub-heading — 上下双线边框 + 紫色居中)

用于 API 更新、技术细节等次级模块标题：

```html
<section style="margin-top:10px;margin-bottom:10px;text-align:center;">
  <section style="display:inline-block;vertical-align:top;padding:3px 0;">
    <section style="width:100%;border-top:1px solid #7774e8;"></section>
    <section style="padding:0 3px;margin:-4px 0;">
      <section style="border-left:1px solid #7774e8;border-right:1px solid #7774e8;padding:3px 6px;color:#7774e8;">
        <p style="margin:0;padding:0;"><b>子标题</b></p>
      </section>
    </section>
    <section style="width:100%;border-top:1px solid #7774e8;"></section>
  </section>
</section>
```

#### Body Paragraph

```html
<p style="white-space:normal;margin:0;padding:0;font-size:15px;line-height:2.2;color:#3e3e3e;text-align:justify;">
  正文段落内容。
</p>
```

#### Bold / Strong

```html
<strong>加粗文字</strong>
```

千问风格不对 strong 加额外颜色，保持正文色。

#### Keyword Highlight (紫色文字 + 浅紫背景)

用于 API 名称、模型名称、技术术语等关键词高亮：

```html
<strong><span style="color:#7774e8;background-color:#e3e2fa;">qwen3.6-max-preview</span></strong>
```

#### Bullet List

```html
<ul style="list-style-type:disc;padding-left:20px;list-style-position:outside;">
  <li><p style="margin:0;padding:0;font-size:15px;line-height:2.2;color:#3e3e3e;">列表项内容</p></li>
</ul>
```

#### Purple Text (无背景强调)

用于特性总结、小标题引导句：

```html
<span style="color:#615ced;font-weight:bold;">Qwen3.6-Max-Preview主要特性包括：</span>
```

#### Image (Full Width)

```html
<section style="text-align:center;">
  <img src="data:image/png;base64,..." alt="描述" style="width:100%;height:auto;">
</section>
```

#### Footer CTA (左 emoji/图 + 右紫色 CTA 文字)

```html
<section style="text-align:left;display:flex;flex-flow:row;">
  <section style="display:inline-block;vertical-align:top;width:20%;align-self:flex-start;flex:0 0 auto;">
    <section style="text-align:center;margin:20px 0 0;line-height:0;">
      <section style="max-width:100%;vertical-align:middle;display:inline-block;line-height:0;width:47%;height:auto;">
        👇
      </section>
    </section>
  </section>
  <section style="display:inline-block;vertical-align:top;width:80%;align-self:flex-start;flex:0 0 auto;">
    <section style="margin:20px 0 0;">
      <section style="text-align:justify;font-size:16px;color:#615ced;">
        <p style="margin:0;padding:0;"><strong>关注我，获取最新动态</strong></p>
      </section>
    </section>
  </section>
</section>
```

#### Lead / Abstract (前言引用块)

```html
<section style="font-size:15px;color:#555;background:#f5f3ff;padding:18px 22px;border-radius:10px;margin:22px 0;line-height:2.2;letter-spacing:1px;">
  前言摘要内容。
</section>
```

#### Closing Block (写在最后)

```html
<section style="background:#f5f3ff;color:#4a1a6b;padding:22px 24px;border-radius:10px;margin:22px 0;line-height:2.2;font-size:15px;letter-spacing:1px;">
  <strong style="color:#7c3aed;">写在最后</strong><br><br>
  结语内容。
</section>
```

#### Footer CTA

```html
<section style="margin-top:35px;padding:22px 25px;background:#f5f3ff;border-radius:10px;text-align:center;line-height:2.0;">
  <p style="font-size:15px;color:#3e3e3e;margin:0 0 8px;letter-spacing:1px;">如果这篇文章对你有帮助，欢迎<strong style="color:#7774e8;">点赞、在看、转发</strong>三连 🙏</p>
  <p style="font-size:13px;color:#888;margin:0;letter-spacing:1px;">关注我，持续分享内容</p>
</section>
```

### Qwen Style Rules

1. **极简风格** — 不用花哨 callout box、不用彩色边框卡片、不用分隔线装饰（但保留前言引用块、写在最后、footer CTA）
2. **紫色单色系** — 只用 `#7774e8` / `#615ced` / `#e3e2fa` 三个紫色系颜色
3. **标题用色块** — H2 居中 + 底部浅紫色块，H3 用上下双线边框
4. **关键词高亮** — 紫色文字 + 浅紫背景，用于 API 名、模型名、技术术语
5. **正文不加 padding** — 段落 margin/padding 都为 0，靠 line-height:2.2 保证呼吸感
6. **图片全宽** — 居中，100% 宽度
7. **不用 emoji** — 千问风格整体偏严肃技术向，正文不使用 emoji（footer CTA 除外）

## Style: aliyun (阿里云风格)

参考阿里云公众号的排版风格。整体偏企业技术博客，蓝色主色调，简洁直接，重内容轻装饰。

### Aliyun Color Palette

```
Primary Blue:       #0033cc  (rgb(0,51,204) — 标题、边框、链接)
Body Text:          #555555  (rgb(85,85,85) — 正文)
Heading Text:       #000000  (rgb(0,0,0) — 标题文字)
Image Border:       #939393  (图片边框)
Background:         #ffffff
```

### Aliyun Typography

```
font-family:   "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", sans-serif
font-size:     15px
line-height:   2
letter-spacing: 1px
text-align:    justify
color:         #555555
padding:       0 8px  (正文两侧留白)
```

### Aliyun Components

#### H1 (Article Title — 默认微信标题样式)

阿里云风格使用微信编辑器自带的 h1，不做特殊处理。

#### H2 (Section Heading — 蓝色粗左边框 + 黑色粗体)

```html
<section style="display:flex;flex-flow:row;margin:10px 0;text-align:left;justify-content:flex-start;">
  <section style="display:inline-block;width:auto;vertical-align:middle;flex:0 0 auto;align-self:center;min-width:10%;max-width:100%;height:auto;border-left:5px solid #0033cc;padding:0 0 0 11px;margin:0;">
    <section style="transform:translate3d(-4px,0,0);">
      <section style="text-align:justify;font-size:16px;color:#000;">
        <p style="margin:0;padding:0;"><strong>章节标题</strong></p>
      </section>
    </section>
  </section>
</section>
```

#### H3 (Sub-heading — # 前缀 + 蓝色斜体粗体)

用于功能介绍、使用指南等次级标题，保留 `#` 前缀作为 markdown 风格视觉暗示：

```html
<p style="margin:0;padding:0;">
  <strong><em><span style="font-size:17px;color:#0033cc;"># 子标题内容</span></em></strong>
</p>
```

#### Body Paragraph

```html
<p style="color:#555;font-size:15px;white-space:normal;margin:0;padding:0;letter-spacing:1px;line-height:2;text-align:justify;">
  正文段落内容。
</p>
```

#### Bold / Strong

```html
<strong>加粗文字</strong>
```

阿里云风格 strong 不加额外颜色，继承正文色。

#### Numbered Sub-item (数字编号段落标题)

用于步骤说明、功能列表等：

```html
<p style="margin:0;padding:0;">
  <strong>1、标题内容</strong>
</p>
<p style="color:#555;font-size:15px;margin:0;padding:0;letter-spacing:1px;line-height:2;">
  描述正文。
</p>
```

#### Bullet List

```html
<ul style="list-style-type:disc;padding-left:20px;list-style-position:outside;">
  <li><p style="margin:0;padding:0;font-size:15px;color:#555;line-height:2;letter-spacing:1px;">列表项内容</p></li>
</ul>
```

#### Italic Link/Resource (斜体资源链接)

用于模型下载地址、文档链接等：

```html
<p style="margin:0;padding:0;">
  <em><span style="font-size:15px;letter-spacing:1px;line-height:2;text-align:justify;color:#000;font-weight:bold;font-style:italic;">资源名</span><span>：https://example.com</span></em>
</p>
```

#### Image (带边框圆角阴影)

```html
<section style="text-align:center;margin-top:10px;margin-bottom:10px;line-height:0;">
  <section style="max-width:100%;vertical-align:middle;display:inline-block;line-height:0;box-shadow:rgba(0,51,204,0.02) 2px 2px 4px 0;border-radius:5px;overflow:hidden;border:0.1px solid #939393;">
    <img src="data:image/png;base64,..." alt="描述" style="vertical-align:middle;max-width:100%;width:100%;height:auto;">
  </section>
</section>
```

#### Code Block (微信原生 code-snippet 样式)

阿里云文章直接使用微信编辑器的原生代码块组件，无需自定义。在生成 HTML 时，使用与默认粉色风格相同的深色背景 `<section>` 方案（因为原生 code-snippet 不能通过 inline HTML 复现）：

```html
<section style="background:#1e293b;border:1px solid #334155;padding:16px 20px;border-radius:8px;font-size:13px;line-height:1.8;margin:18px 8px;color:#e2e8f0;">
  export DASHSCOPE_API_KEY=&lt;your_api_key&gt;<br>
  pip install dashscope
</section>
```

#### Lead / Abstract (前言引用块)

```html
<section style="font-size:15px;color:#555;background:#f0f4ff;padding:18px 22px;border-left:4px solid #0033cc;margin:22px 0;line-height:2;letter-spacing:1px;">
  前言摘要内容。
</section>
```

#### Closing Block (写在最后)

```html
<section style="background:#f0f4ff;border-left:4px solid #0033cc;padding:22px 24px;margin:22px 0;line-height:2;font-size:15px;color:#333;letter-spacing:1px;">
  <strong style="color:#0033cc;">写在最后</strong><br><br>
  结语内容。
</section>
```

#### Footer CTA

```html
<section style="margin-top:35px;padding:22px 25px;background:#f0f4ff;border-radius:10px;text-align:center;line-height:2.0;">
  <p style="font-size:15px;color:#555;margin:0 0 8px;letter-spacing:1px;">如果这篇文章对你有帮助，欢迎<strong style="color:#0033cc;">点赞、在看、转发</strong>三连 🙏</p>
  <p style="font-size:13px;color:#888;margin:0;letter-spacing:1px;">关注我，持续分享内容</p>
</section>
```

### Aliyun Style Rules

1. **极简务实** — 不用 callout box、不用彩色背景块、不用花哨分隔线
2. **蓝色单色系** — 只用 `#0033cc` 作为唯一强调色
3. **H2 用蓝色左边框** — 5px 粗蓝色左边框 + 黑色粗体 16px
4. **H3 用 # 前缀** — 蓝色斜体粗体 17px，保留 markdown `#` 符号
5. **正文灰色** — `#555` 而非纯黑，降低对比度减轻阅读疲劳
6. **图片有边框** — `border-radius:5px` + 浅灰边框 + 微弱 box-shadow，区别于正文
7. **不用 emoji** — 纯企业技术风格
8. **数字编号段落** — 用 `1、` `2、` 而不是 bullet list 做功能介绍

## Style: aws (亚马逊云科技风格)

参考亚马逊云开发者公众号的排版风格。橙色主色调，方框标题带阴影，企业级技术教程感。

### AWS Color Palette

```
Primary Orange:     #ff9900  (rgb(255,153,0) — 标题阴影、橙色块、链接名、渐变)
Body Text:          #3e3e3e  (rgb(62,62,62) — 正文)
Secondary Text:     #565656  (rgb(86,86,86) — H3 标题文字)
Code Highlight:     #e60f0b  (rgb(230,15,11) — inline code 文字色)
Code Highlight BG:  #e4e4e4  (rgb(228,228,228) — inline code 背景)
Border Dark:        #3e3e3e  (rgb(62,62,62) — 标题边框、H3 顶部线)
Border Light:       #808080  (虚线边框)
Card Border:        #efefef  (rgb(239,239,239) — 链接卡片外框)
Footer Border:      #fcf3e9  (rgb(252,243,233) — 底部 callout 边框)
Background:         #ffffff
```

### AWS Typography

```
font-family:   system-ui, -apple-system, BlinkMacSystemFont, Arial, sans-serif
font-size:     16px
line-height:   2
letter-spacing: 1px
text-align:    justify
color:         #3e3e3e
padding:       0 8px
```

### AWS Components

#### H1 (Article Title — 默认微信标题样式)

AWS 风格使用微信编辑器自带的 h1。

#### H2 (Section Heading — 方框 + 橙色阴影)

居中显示，1px 实线边框 + 右下 6px 橙色 box-shadow：

```html
<section style="justify-content:center;display:flex;flex-flow:row;margin:10px 0 20px;">
  <section style="display:inline-block;width:auto;vertical-align:top;align-self:flex-start;flex:0 0 auto;border:1px solid #3e3e3e;min-width:5%;max-width:100%;height:auto;box-shadow:#ff9900 6px 6px 0 0;padding:8px;">
    <section style="margin:0;">
      <section style="font-size:18px;line-height:1.6;">
        <p style="margin:0;padding:0;text-align:left;"><strong>章节标题</strong></p>
      </section>
    </section>
  </section>
</section>
```

#### H3 (Sub-heading — 橙色竖条 + 顶部横线)

左侧橙色小方块 + 顶部深色 1px 线 + 右侧和底部边框：

```html
<section style="margin:10px 0;display:flex;flex-flow:row;justify-content:flex-start;">
  <section style="margin:13px 0 0;padding:3px 3px 0 0;display:inline-block;vertical-align:top;width:13px;box-shadow:#fff -3px 3px 0 inset;border-top:1px solid #3e3e3e;border-right:1px solid #3e3e3e;background-color:#ff9900;flex:0 0 auto;height:auto;align-self:stretch;border-bottom-width:0;"></section>
  <section style="padding:0 0 0 20px;display:inline-block;vertical-align:top;width:auto;border-width:0 1px 1px;border-right:1px solid #3e3e3e;border-bottom:1px solid #3e3e3e;flex:0 0 auto;align-self:stretch;min-width:10%;height:auto;">
    <section style="background-color:#3e3e3e;height:1px;"></section>
    <section style="transform:translate3d(-10px,0,0);">
      <section style="color:#565656;line-height:2;">
        <p style="margin:0;padding:0;text-align:left;"><strong>子标题内容</strong></p>
      </section>
    </section>
  </section>
</section>
```

#### Body Paragraph

```html
<p style="color:#3e3e3e;font-size:16px;white-space:normal;margin:0;padding:0;letter-spacing:1px;line-height:2;text-align:left;">
  正文段落内容。
</p>
```

#### Inline Code Highlight (红色文字 + 灰色背景)

用于 API 名称、命令、技术术语：

```html
<span style="color:#e60f0b;background-color:#e4e4e4;">Responses</span>
```

#### Bullet List

```html
<ul style="list-style-type:disc;padding-left:20px;list-style-position:outside;">
  <li><p style="margin:0;padding:0;font-size:16px;color:#3e3e3e;line-height:2;letter-spacing:1px;">列表项内容</p></li>
</ul>
```

#### Link Card (虚线边框引用卡片)

用于相关文档、参考链接等：

```html
<section style="margin:10px 0;">
  <section style="border:3px solid #efefef;padding:3px;">
    <section style="border:1px dashed #808080;padding:0 10px;font-size:12px;color:#999;">
      <p style="margin:0;padding:0;text-align:left;"><strong><span style="color:#ff9900;">资源名称：</span></strong></p>
      <p style="margin:0;padding:0;text-align:left;">https://example.com/link</p>
    </section>
  </section>
</section>
```

#### Author Card (作者介绍 — 橙色方块 + 渐变横线)

```html
<section style="display:flex;flex-flow:row;margin:10px 0 15px;">
  <section style="display:inline-block;vertical-align:bottom;width:auto;align-self:flex-end;flex:0 0 0%;height:auto;">
    <section style="display:flex;flex-flow:row;">
      <section style="display:inline-block;width:28px;vertical-align:top;height:28px;overflow:hidden;background-color:#ff9900;"></section>
    </section>
  </section>
  <section style="display:inline-block;vertical-align:bottom;width:auto;align-self:flex-end;flex:0 0 0%;line-height:0;">
    <section style="transform:translate3d(-10px,0,0);">
      <section style="font-size:18px;color:#3e3e3e;">
        <p style="margin:0;padding:0;"><strong>本篇作者</strong></p>
      </section>
    </section>
  </section>
  <section style="display:inline-block;vertical-align:bottom;align-self:flex-end;flex:1 1 0%;height:auto;overflow:hidden;">
    <section style="height:4px;overflow:hidden;background-image:linear-gradient(270deg,#fff 0%,#ff9900 78%);"></section>
  </section>
</section>
```

#### Footer Callout (橙色渐变边框 + 浅橙边框卡片)

```html
<section style="border:1px solid #fcf3e9;margin:3px 0;background-color:#fff;padding:19px;">
  <section style="font-size:14px;">
    <p style="margin:0;padding:0;text-align:left;">
      底部提示或推广内容。
    </p>
  </section>
</section>
```

#### Image (全宽无边框)

```html
<section style="text-align:center;">
  <img src="data:image/png;base64,..." alt="描述" style="width:100%;height:auto;">
</section>
```

#### Lead / Abstract (前言引用块)

```html
<section style="font-size:16px;color:#3e3e3e;background:#fff8ef;padding:18px 22px;border-left:4px solid #ff9900;margin:22px 0;line-height:2;letter-spacing:1px;">
  前言摘要内容。
</section>
```

#### Closing Block (写在最后)

```html
<section style="background:#fff8ef;border-left:4px solid #ff9900;padding:22px 24px;margin:22px 0;line-height:2;font-size:16px;color:#3e3e3e;letter-spacing:1px;">
  <strong>写在最后</strong><br><br>
  结语内容。
</section>
```

#### Footer CTA

```html
<section style="margin-top:35px;padding:22px 25px;background:#fff8ef;border-radius:10px;text-align:center;line-height:2.0;">
  <p style="font-size:16px;color:#3e3e3e;margin:0 0 8px;letter-spacing:1px;">如果这篇文章对你有帮助，欢迎<strong style="color:#ff9900;">点赞、在看、转发</strong>三连 🙏</p>
  <p style="font-size:13px;color:#888;margin:0;letter-spacing:1px;">关注我，持续分享内容</p>
</section>
```

### AWS Style Rules

1. **橙色主调** — 唯一强调色 `#ff9900`（AWS 品牌橙）
2. **H2 方框阴影** — 1px 边框 + 右下 6px 橙色阴影，居中显示
3. **H3 橙色竖条** — 左侧小橙色块 + 顶部横线 + 右下边框，左对齐
4. **Inline code 红色** — `#e60f0b` 文字 + `#e4e4e4` 灰色背景，用于 API 名、命令
5. **链接卡片** — 外层 3px 浅灰边框 + 内层虚线边框，链接名橙色粗体
6. **作者卡片** — 橙色方块 + 渐变横线 + 圆形头像
7. **正文 16px** — 比其他风格稍大，letter-spacing:1px
8. **不用 emoji** — 纯企业技术风格
9. **不用 callout box** — 强调内容用方框阴影标题或链接卡片

## Component Library (Default Pink Style)

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

## Document Templates

### Default (Pink) Template

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

### Qwen Template

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Article Title</title>
</head>
<body style="font-family:PingFangSC-light,'PingFang SC','Hiragino Sans GB','Microsoft YaHei',sans-serif;font-size:15px;line-height:2.2;color:#3e3e3e;max-width:680px;margin:0 auto;padding:20px 0;background:#fff;text-align:justify;">
... content ...
</body>
</html>
```

### Aliyun Template

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Article Title</title>
</head>
<body style="font-family:'PingFang SC','Hiragino Sans GB','Microsoft YaHei',sans-serif;font-size:15px;line-height:2;color:#555;letter-spacing:1px;max-width:680px;margin:0 auto;padding:20px 0;background:#fff;text-align:justify;">
<section style="padding:0 8px;">
... content ...
</section>
</body>
</html>
```

### AWS Template

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Article Title</title>
</head>
<body style="font-family:system-ui,-apple-system,BlinkMacSystemFont,Arial,sans-serif;font-size:16px;line-height:2;color:#3e3e3e;letter-spacing:1px;max-width:680px;margin:0 auto;padding:20px 0;background:#fff;text-align:justify;">
<section style="padding:0 8px;">
... content ...
</section>
</body>
</html>
```

## Available Styles

| Style | Trigger | Theme |
|-------|---------|-------|
| default | No style specified, or "pink" | 粉色主题，多色辅助，callout 丰富，适合生活化/教程类文章 |
| qwen | User specifies "qwen" or "千问风格" | 紫色单色系，极简技术感，适合技术发布/产品介绍类文章 |
| aliyun | User specifies "aliyun" or "阿里云风格" | 蓝色单色系，企业技术博客风，适合产品发布/开源公告/功能介绍类文章 |
| aws | User specifies "aws" or "亚马逊风格" | 橙色主调，方框阴影标题，企业级技术教程风，适合技术教程/产品上手/实战指南类文章 |

## Steps

1. **Determine style**: Check if `$ARGUMENTS` or user message specifies a style name (e.g. "qwen", "千问风格", "aliyun", "阿里云风格", "aws", "亚马逊风格"). If not specified, use `default` (pink) style.

2. **Determine input**: If `$ARGUMENTS` contains a file path, read it. If it's a topic description, use it as the article theme.

3. **If markdown file**: Convert to WeChat-compatible HTML using the selected style's components. Process:
   - **Default style**:
     - `# H1` → centered title with tagline
     - `## H2` → numbered section headings with pink badges
     - `### H3` → subsection headings
     - `> blockquote` → callout boxes (choose color by context)
     - `**bold**` → `<strong>` with `color:#2b2b2b`
     - Tables → inline-styled tables with pink borders
     - Code blocks → dark bg `<section>` with `<br>` line breaks and `&nbsp;` indentation (NO `<pre>`, NO `white-space:pre`, NO syntax highlighting spans)
     - Images → base64 encode if local file exists, full-width
     - Lists → styled `<p>` with bullet characters
     - `---` → dot separator
   - **Qwen style**:
     - `# H1` → 默认微信标题，不做特殊处理
     - `## H2` → 居中紫色文字 + 底部浅紫色块
     - `### H3` → 上下双线边框 + 紫色居中文字
     - `> blockquote` → 不使用 callout box，转为紫色文字强调或直接作为正文
     - `**bold**` → `<strong>` 不加额外颜色
     - API 名/模型名/技术术语 → 紫色文字 + 浅紫背景高亮
     - Tables → 保持简洁，无彩色边框
     - Code blocks → dark bg `<section>` with `<br>` and `&nbsp;` (same rules as default)
     - Images → 居中全宽
     - Lists → `<ul>` with `disc` bullets
     - 不使用 emoji（footer CTA 除外）
   - **Aliyun style**:
     - `# H1` → 默认微信标题，不做特殊处理
     - `## H2` → 蓝色 5px 左边框 + 黑色粗体 16px
     - `### H3` → `# 标题` 格式，蓝色斜体粗体 17px
     - `> blockquote` → 不使用 callout box，转为正文或粗体强调
     - `**bold**` → `<strong>` 不加额外颜色
     - Tables → 保持简洁
     - Code blocks → dark bg `<section>` with `<br>` and `&nbsp;` (same rules as default)
     - Images → 居中，带边框圆角阴影
     - Lists → `<ul>` with `disc` bullets
     - 资源链接 → 斜体，名称加粗黑色
     - 不使用 emoji
   - **AWS style**:
     - `# H1` → 默认微信标题，不做特殊处理
     - `## H2` → 方框 + 右下 6px 橙色阴影，居中
     - `### H3` → 橙色竖条 + 顶部横线 + 右下边框
     - `> blockquote` → 不使用 callout box，用方框阴影或链接卡片替代
     - `**bold**` → `<strong>` 不加额外颜色
     - Inline code / API 名 / 命令 → 红色文字 + 灰色背景高亮
     - Tables → 保持简洁
     - Code blocks → dark bg `<section>` with `<br>` and `&nbsp;` (same rules as default)
     - Images → 居中全宽
     - Lists → `<ul>` with `disc` bullets
     - 参考链接 → 虚线边框链接卡片，链接名橙色粗体
     - 不使用 emoji

4. **If topic description**: Write the article content first, then convert to HTML.

5. **Wrap in full HTML document** using the selected style's template.

6. **Save output**: Write to the same directory as input, or to `~/clawd/gzh/<topic-slug>/` if creating from scratch. Filename: `<title>-wx.html`

7. **Report**: Show file path, size, and remind user to:
   - Open in browser to preview
   - Select all inside `<body>` and copy into WeChat editor
   - Base64 images may need re-upload via WeChat image tool if they don't render

## Writing Style

### Default (Pink) Style
- 朋友聊天式，不要机构感，不要 AI 味
- 技术内容要准确但不枯燥
- 适当使用 emoji 但不过度（每个 section 最多 1-2 个）
- 段落不要太长——每段 3-5 行为宜，移动端阅读体验优先
- 每个 section 之间留足视觉间距（用 separator 或 margin）
- 用短句，少用长定语从句
- 口语化表达优先："说白了"、"你想"、"等等" > "本质上"、"从角度看"、"值得注意的是"

### Qwen Style
- 偏正式技术风格，但不死板——像技术博客，不是论文
- 不使用 emoji（footer CTA 除外）
- 段落可以稍长，但保持逻辑清晰
- 技术术语直接使用，不需要过多解释
- 适合产品发布、技术更新、Benchmark 对比类文章
- 结构清晰：背景介绍 → 核心特性 → 性能数据 → 使用方式 → 总结

### Aliyun Style
- 企业技术博客风格，直奔主题，不废话
- 不使用 emoji
- 段落简洁有力，一段说一件事
- 数字编号做功能/步骤介绍（1、2、3、）
- 资源链接用斜体列出，名称加粗
- 适合开源发布、产品功能介绍、企业技术公告类文章
- 结构清晰：开篇一句话总结 → 核心亮点 → 性能数据 → 开始使用 → 总结

### AWS Style
- 技术教程风格，手把手带读者上手
- 不使用 emoji
- 正文 16px，比其他风格稍大，阅读舒适
- 代码示例丰富，步骤拆解清晰
- 相关文档用链接卡片呈现（虚线边框+橙色链接名）
- 文末附作者介绍（橙色方块+渐变横线+圆形头像）
- 适合技术教程、产品上手指南、实战演练类文章
- 结构清晰：概述 → 各功能/步骤分块（方框标题） → 代码示例 → 相关文档 → 作者介绍
