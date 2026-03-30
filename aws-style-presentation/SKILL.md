---
name: aws-style-presentation
description: Create AWS-style HTML slide presentations with purple/pink gradient theme, card layouts, data comparison tables, badge labels, progress bar navigation, and slide-in animations. Use this skill whenever the user wants to create a presentation, PPT, slides, tech talk, product intro, test report, or solution comparison. Triggers on phrases like "make a PPT", "create presentation", "slides", "tech sharing", "product intro", "做PPT", "做演示", "技术分享", "产品介绍".
---

# AWS-Style HTML Presentation Skill

Generate zero-dependency, single-file HTML slide presentations with a professional purple-pink gradient visual style. All CSS and JS are inlined — no external frameworks required.

## Color System

All presentations must define and use these CSS custom properties:

```css
:root {
    --aws-orange: #FF9900;
    --aws-purple: #8B46FF;
    --aws-pink: #FF4F8B;
    --aws-dark: #232F3E;
    --aws-blue: #146EB4;
    --aws-gray: #545B64;
    --aws-light-gray: #F2F3F3;
    --success: #16a34a;
    --warning: #f59e0b;
    --danger: #dc2626;
}
```

Color usage rules:
- Body background: `linear-gradient(135deg, var(--aws-purple), var(--aws-pink))`
- Slide background: `rgba(255, 255, 255, 0.98)` (semi-transparent white)
- h1: `var(--aws-purple)` with 4px `var(--aws-pink)` bottom border
- h2: `var(--aws-purple)` | h3: `var(--aws-pink)`
- Body text / list items: `var(--aws-gray)`
- Emphasis blocks: light purple bg `rgba(139,70,255,0.1)` + purple left border

## Typography

```css
font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
```

Font sizes: h1=2.2em(weight:700) | h2=1.4em | h3=1.2em | body=0.95em(line-height:1.5) | small=0.85em

## Core Components

### 1. Slide Container (.slide)

Absolutely positioned fullscreen panels with horizontal slide-in transition:

```css
.slide {
    position: absolute; width: 100vw; min-height: 100vh; max-height: 100vh;
    background: rgba(255,255,255,0.98); padding: 40px 60px 80px 60px;
    left: 0; top: 0; transform: translateX(100%); opacity: 0;
    transition: all 0.6s ease; overflow-y: auto; color: var(--aws-dark);
}
.slide.active { opacity: 1; transform: translateX(0); z-index: 10; }
```

### 2. Card (.card)

Light gray background with colored left border for feature showcases and categorized info:

```css
.card {
    background: var(--aws-light-gray); padding: 12px; border-radius: 8px;
    margin: 8px 0; border-left: 4px solid var(--aws-pink);
}
```

Left border color semantics: pink=default | green=advantage | red=disadvantage | yellow=caution | blue=info

### 3. Grid (.grid)

Responsive grid for side-by-side card layouts:

```css
.grid {
    display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 12px; margin: 10px 0;
}
```

### 4. Badge (.badge)

Pill-shaped status labels:

```css
.badge {
    display: inline-block; background: var(--aws-blue); color: white;
    padding: 4px 12px; border-radius: 20px; font-size: 0.8em; font-weight: 600;
}
.badge.ga { background: #16a34a; }
.badge.preview { background: var(--aws-orange); }
.badge.warning { background: #f59e0b; }
.badge.danger { background: #dc2626; }
```

### 5. Comparison Table (.comparison-table)

Purple-pink gradient header with highlight rows for data comparison:

```css
.comparison-table { width: 100%; border-collapse: collapse; font-size: 0.95em; }
.comparison-table th {
    background: linear-gradient(135deg, var(--aws-purple), var(--aws-pink));
    color: white; padding: 10px; text-align: left;
}
.comparison-table td { padding: 10px; border-bottom: 1px solid #ddd; }
.comparison-table tr:nth-child(even) { background: var(--aws-light-gray); }
.comparison-table tr.highlight-row { background: rgba(139,70,255,0.15); font-weight: 600; }
```

Use `<strong style="color: var(--aws-purple);">` for best-in-class values. Embed `.badge` for status indicators.

### 6. Metric Box (.metric-box)

Purple-pink gradient box for key numbers and statistics:

```css
.metric-box {
    display: inline-block;
    background: linear-gradient(135deg, var(--aws-purple), var(--aws-pink));
    color: white; padding: 10px 18px; border-radius: 8px;
    font-size: 1.2em; font-weight: 700; margin: 8px 8px 8px 0;
}
```

### 7. Emphasis Block (.emphasis)

Bottom-of-slide summary callout:

```css
.emphasis {
    font-size: 1em; font-weight: 600; color: var(--aws-purple);
    margin: 12px 0; padding: 10px;
    background: rgba(139,70,255,0.1); border-left: 4px solid var(--aws-purple);
    border-radius: 4px;
}
```

### 8. Highlight Text (.highlight)

Inline highlighted text span:

```css
.highlight {
    background: rgba(139,70,255,0.15); padding: 2px 6px;
    border-radius: 4px; color: var(--aws-dark); font-weight: 600;
}
```

## Slide Types

### Title Slide (Cover)

Centered layout with large h1 (3.5em, no border) + pink h2 subtitle (2em) + author info:

```html
<div class="slide active">
  <div style="display:flex;flex-direction:column;justify-content:center;
              align-items:center;height:80vh;text-align:center;">
    <h1 style="font-size:3.5em;margin-bottom:40px;border:none;">Main Title</h1>
    <h2 style="font-size:2em;color:var(--aws-pink);margin-bottom:60px;">Subtitle</h2>
    <div style="margin-top:80px;font-size:1.3em;color:var(--aws-gray);">
      <p><strong>Author | Department | Role</strong></p>
    </div>
  </div>
</div>
```

### Content Slide

Structure: h1 title → h2 section → component combinations (grid+card / table / list) → .emphasis summary

### Closing Slide (Q&A)

Centered h1 (4em, pink) "Q & A" + thank-you message

## Navigation System

### Bottom Navigation Bar

Semi-transparent purple pill with pink circular buttons:

```css
.navigation {
    position: fixed; bottom: 25px; left: 50%; transform: translateX(-50%);
    display: flex; gap: 10px; z-index: 100;
    background: rgba(139,70,255,0.95); padding: 10px 20px; border-radius: 50px;
}
.nav-btn {
    background: var(--aws-pink); border: none; color: white;
    width: 40px; height: 40px; border-radius: 50%;
    cursor: pointer; font-size: 1.2em; transition: all 0.3s ease;
}
.nav-btn:hover { background: var(--aws-purple); transform: scale(1.1); }
```

### Progress Bar

Top-fixed 4px purple-pink gradient bar, width updates with slide index.

### Slide Counter

Top-right purple pill showing "current / total".

### Logo

Top-left fixed white bold text. Replace with user-provided logo text if given.

## Animation System

### Slide Transition

Horizontal slide-in with 0.6s ease (translateX + opacity).

### Staggered Content Entry

Each direct child of the active slide fades up with incremental delay:

```css
@keyframes slideIn {
    from { opacity: 0; transform: translateY(30px); }
    to { opacity: 1; transform: translateY(0); }
}
.slide.active > * { animation: slideIn 0.5s ease-out backwards; }
.slide.active > *:nth-child(1) { animation-delay: 0.1s; }
.slide.active > *:nth-child(2) { animation-delay: 0.2s; }
.slide.active > *:nth-child(3) { animation-delay: 0.3s; }
.slide.active > *:nth-child(4) { animation-delay: 0.4s; }
.slide.active > *:nth-child(5) { animation-delay: 0.5s; }
```

## JavaScript Navigation

Implement full keyboard (ArrowRight/Space/PageDown for next, ArrowLeft/PageUp for prev, Home/End), button click, and touch swipe (50px threshold) navigation. Update progress bar width and slide counter text on every transition:

```javascript
let currentSlide = 0;
const slides = document.querySelectorAll('.slide');
const totalSlides = slides.length;

function updateSlide() {
    slides.forEach((slide, index) => {
        slide.classList.remove('active');
        if (index === currentSlide) {
            slide.classList.add('active');
            slide.scrollTop = 0;
        }
    });
    document.getElementById('slideCounter').textContent =
        `${currentSlide + 1} / ${totalSlides}`;
    document.getElementById('progressBar').style.width =
        `${((currentSlide + 1) / totalSlides) * 100}%`;
}
function nextSlide() {
    if (currentSlide < totalSlides - 1) { currentSlide++; updateSlide(); }
}
function prevSlide() {
    if (currentSlide > 0) { currentSlide--; updateSlide(); }
}
```

## Print Styles

Include `@media print` for PDF export support:
- All slides: `position: relative`, `opacity: 1`, `transform: none`, `page-break-after: always`
- Hide: navigation, progress-bar, slide-counter, logo
- Cards: `break-inside: avoid`

## Emoji Usage

Use emoji in h2/h3 headings and card titles to add visual hierarchy:
🚀 performance | ⚡ speed | 🧠 AI/intelligence | 🎨 design | 🎯 precision | ✅ recommended | ❌ not recommended | ⚠️ caution | 💡 tip/suggestion | 📋 rules | 📄 docs | 💾 cache | 📦 batch | 🔗 link | 🎙️ voice | 🆕 new feature

## Generation Rules

1. Output a single self-contained HTML file with all CSS/JS inlined
2. First slide = title/cover slide, last slide = Q&A or summary slide
3. Keep content density moderate per slide — prefer more slides over cramming
4. Use `.comparison-table` for data comparisons, `.card` + `.grid` for feature showcases
5. Use `.emphasis` at the bottom of each slide for a one-line takeaway
6. Highlight best values in tables with purple bold, annotate status with `.badge`
7. Match card left-border color to semantic meaning (green=good, red=bad, yellow=caution)
8. Keyboard, touch, and button navigation must all be fully implemented
9. Support both Chinese and English content — set `lang` attribute accordingly
10. Replace top-left `.logo` text when user provides a custom logo name
