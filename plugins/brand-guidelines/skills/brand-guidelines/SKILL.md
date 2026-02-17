---
name: applying-brand-guidelines
description: Applies Anthropic's official brand colors, typography, and visual hierarchy to presentations, documents, slides, and other visual artifacts. Activates when the user requests Anthropic-branded styling or needs consistent brand identity across content.
---

# Anthropic Brand Styling Guide

This skill applies Anthropic's official brand colors and typography to any visual artifact that benefits from Anthropic's look-and-feel.

## Brand Color Palette

### Primary Colors

| Name | Hex | Usage |
|------|-----|-------|
| Dark | `#141413` | Primary text, headers |
| Light | `#FAF9F5` | Backgrounds, light mode |
| Mid Gray | `#B0AEA5` | Secondary text, borders |
| Light Gray | `#E8E6DC` | Subtle backgrounds, dividers |

### Accent Colors

| Name | Hex | Usage |
|------|-----|-------|
| Orange | `#D97757` | Primary accent, CTAs, highlights |
| Blue | `#6A9BCC` | Links, secondary accent |
| Green | `#788C5D` | Success states, tertiary accent |

## Typography Standards

### Fonts

| Element | Primary Font | Fallback |
|---------|--------------|----------|
| Headings (24pt+) | Poppins | Arial |
| Body text | Lora | Georgia |

### Font Sizes

| Element | Size |
|---------|------|
| H1 | 36pt |
| H2 | 28pt |
| H3 | 24pt |
| Body | 12pt |
| Caption | 10pt |

## Application Rules

### Automatic Styling

When applying brand guidelines:

1. **Headings** (24pt and larger): Apply Poppins font
2. **Body text**: Apply Lora font
3. **Accent colors**: Cycle through for non-text shapes
4. **Backgrounds**: Use Light (#FAF9F5) for slides/pages

### Color Application Order

For multiple accent elements, cycle through:
1. Orange (`#D97757`)
2. Blue (`#6A9BCC`)
3. Green (`#788C5D`)

## Implementation

### PowerPoint (python-pptx)

```python
from pptx.util import Pt
from pptx.dml.color import RGBColor

# Brand colors
ANTHROPIC_DARK = RGBColor(0x14, 0x14, 0x13)
ANTHROPIC_LIGHT = RGBColor(0xFA, 0xF9, 0xF5)
ANTHROPIC_ORANGE = RGBColor(0xD9, 0x77, 0x57)
ANTHROPIC_BLUE = RGBColor(0x6A, 0x9B, 0xCC)
ANTHROPIC_GREEN = RGBColor(0x78, 0x8C, 0x5D)

def apply_heading_style(shape):
    """Apply brand styling to heading text."""
    for paragraph in shape.text_frame.paragraphs:
        for run in paragraph.runs:
            run.font.name = 'Poppins'
            run.font.color.rgb = ANTHROPIC_DARK

def apply_body_style(shape):
    """Apply brand styling to body text."""
    for paragraph in shape.text_frame.paragraphs:
        for run in paragraph.runs:
            run.font.name = 'Lora'
            run.font.color.rgb = ANTHROPIC_DARK
```

### CSS

```css
:root {
    --anthropic-dark: #141413;
    --anthropic-light: #FAF9F5;
    --anthropic-mid-gray: #B0AEA5;
    --anthropic-light-gray: #E8E6DC;
    --anthropic-orange: #D97757;
    --anthropic-blue: #6A9BCC;
    --anthropic-green: #788C5D;
}

body {
    background-color: var(--anthropic-light);
    color: var(--anthropic-dark);
    font-family: 'Lora', Georgia, serif;
}

h1, h2, h3, h4 {
    font-family: 'Poppins', Arial, sans-serif;
}

a {
    color: var(--anthropic-blue);
}

.accent {
    color: var(--anthropic-orange);
}

.cta-button {
    background-color: var(--anthropic-orange);
    color: var(--anthropic-light);
}
```

### Word Documents (docx)

```python
from docx.shared import Pt, RGBColor

def apply_brand_heading(paragraph):
    """Apply brand styling to heading."""
    for run in paragraph.runs:
        run.font.name = 'Poppins'
        run.font.color.rgb = RGBColor(0x14, 0x14, 0x13)

def apply_brand_body(paragraph):
    """Apply brand styling to body text."""
    for run in paragraph.runs:
        run.font.name = 'Lora'
        run.font.color.rgb = RGBColor(0x14, 0x14, 0x13)
```

## Font Installation

### Poppins

Download from [Google Fonts](https://fonts.google.com/specimen/Poppins):
- Regular (400)
- Medium (500)
- SemiBold (600)
- Bold (700)

### Lora

Download from [Google Fonts](https://fonts.google.com/specimen/Lora):
- Regular (400)
- Medium (500)
- SemiBold (600)

### Fallback Behavior

If custom fonts aren't installed:
- Poppins → Arial (system-installed)
- Lora → Georgia (system-installed)

This ensures brand consistency even without font installation.

## Visual Hierarchy

### Slide Design

```
┌──────────────────────────────────────┐
│ [#FAF9F5 Background]                 │
│                                      │
│  HEADING (Poppins, #141413, 36pt)    │
│                                      │
│  Body text uses Lora font at 12pt    │
│  with Anthropic Dark (#141413) color │
│                                      │
│  ┌──────────────────┐                │
│  │ [#D97757 Accent] │                │
│  │ Shape/Button     │                │
│  └──────────────────┘                │
│                                      │
└──────────────────────────────────────┘
```

### Document Layout

```
Title (Poppins, 36pt, #141413)
═══════════════════════════════

Section Header (Poppins, 24pt, #141413)
───────────────────────────────

Body text in Lora, 12pt, with proper line
spacing (1.5) for readability. Links appear
in Anthropic Blue (#6A9BCC).

> Callouts use Light Gray background (#E8E6DC)
> with Orange accent border (#D97757)
```

## Best Practices

1. **Consistency**: Apply brand styling uniformly across all elements
2. **Contrast**: Ensure 4.5:1 minimum contrast ratio
3. **Whitespace**: Use generous margins with Light background
4. **Accent sparingly**: Reserve Orange for key calls-to-action
5. **Fallbacks**: Always specify fallback fonts for cross-platform compatibility

## License

See LICENSE.txt for complete terms.
