---
name: pptx
description: Use this skill when working with PowerPoint presentations (.pptx files) - creating slides, editing existing presentations, adding charts/tables, or converting HTML to PPTX.
---

# PowerPoint (.pptx) Guide

This guide covers creating, editing, and analyzing PowerPoint presentations. For HTML-to-PPTX conversion, see [html2pptx.md](references/html2pptx.md). For OOXML technical details, see [ooxml.md](references/ooxml.md).

## Workflows Overview

| Task | Approach | Reference |
|------|----------|-----------|
| Read/analyze slides | Markdown conversion or raw XML | This file |
| Create from HTML | html2pptx library | html2pptx.md |
| Create from template | Python scripts | This file |
| Edit existing PPTX | Unpack/modify/repack | ooxml.md |

**Important**: .pptx files are ZIP archives containing XML and resources.

## Reading & Analysis

### Convert to Markdown

```bash
# Using markitdown
markitdown presentation.pptx > slides.md
```

### Access Raw XML

```bash
# Unpack
python ooxml/scripts/unpack.py presentation.pptx unpacked/

# View slide content
cat unpacked/ppt/slides/slide1.xml

# View speaker notes
cat unpacked/ppt/notesSlides/notesSlide1.xml
```

### Create Thumbnail Grid

```bash
python scripts/thumbnail.py presentation.pptx thumbnails/
```

Creates a visual grid showing all slides for quick analysis.

## Creating New Presentations

### Using html2pptx (Recommended)

1. Create HTML slides with proper dimensions
2. Convert using html2pptx library
3. Add charts/data programmatically

See [html2pptx.md](references/html2pptx.md) for complete guide.

### Using Template

```python
# 1. Analyze template
python scripts/inventory.py template.pptx > inventory.json

# 2. Create replacements JSON
# Map placeholder text to new content

# 3. Apply replacements
python scripts/replace.py template.pptx replacements.json output.pptx
```

## Design Principles

### Required
- State design approach before writing code
- Use **web-safe fonts only**: Arial, Helvetica, Times New Roman, Georgia, Courier New, Verdana, Tahoma, Trebuchet MS
- Ensure text contrast ratio ≥ 4.5:1 for readability
- Validate output immediately after creation

### Recommended
- Content-informed design (colors/style match topic)
- Consistent visual hierarchy across slides
- Maximum 6 bullet points per slide
- Meaningful use of whitespace

## Example Color Palettes

Choose palette based on content:

| Style | Background | Primary | Secondary | Accent |
|-------|------------|---------|-----------|--------|
| Corporate | FFFFFF | 2C3E50 | 34495E | 3498DB |
| Creative | F5F5F5 | E74C3C | F39C12 | 9B59B6 |
| Tech | 1A1A2E | 16213E | 0F3460 | E94560 |
| Nature | F0F4F0 | 27AE60 | 2ECC71 | 16A085 |
| Elegant | FAF9F5 | 141413 | B0AEA5 | D97757 |

## Supporting Scripts

### thumbnail.py
Creates thumbnail grids for visual analysis.

```bash
python scripts/thumbnail.py input.pptx output_dir/ [--columns 5]
```

### inventory.py
Extracts all text shapes with properties.

```bash
python scripts/inventory.py input.pptx > inventory.json
```

Output includes:
- Slide number and shape ID
- Text content and formatting
- Position (x, y) and dimensions
- Overflow detection

### replace.py
Applies text replacements from JSON.

```bash
python scripts/replace.py input.pptx replacements.json output.pptx
```

Replacements format:
```json
{
  "slide_1": {
    "shape_2": "New title text",
    "shape_5": "Updated bullet points"
  }
}
```

### rearrange.py
Duplicates, reorders, or deletes slides.

```bash
python scripts/rearrange.py input.pptx operations.json output.pptx
```

Operations format (0-indexed):
```json
{
  "operations": [
    {"action": "duplicate", "source": 0, "count": 2},
    {"action": "delete", "index": 5},
    {"action": "move", "from": 3, "to": 1}
  ]
}
```

### validate.py
Validates PPTX structure and content.

```bash
python ooxml/scripts/validate.py output.pptx
```

Checks:
- XML well-formedness
- Required files present
- Relationship integrity
- Content overflow

## Editing Existing Presentations

### Workflow

1. **Unpack**: `python ooxml/scripts/unpack.py input.pptx unpacked/`
2. **Modify**: Edit XML files directly
3. **Validate**: `python ooxml/scripts/validate.py unpacked/`
4. **Repack**: `python ooxml/scripts/pack.py unpacked/ output.pptx`

### Common Modifications

- Text changes: Edit `<a:t>` elements in slide XML
- Add images: Add to `ppt/media/`, update relationships
- Reorder slides: Modify `presentation.xml` `<p:sldIdLst>`

See [ooxml.md](references/ooxml.md) for detailed XML patterns.

## Dependencies

```bash
# Python
pip install markitdown python-pptx Pillow defusedxml

# JavaScript (for html2pptx)
npm install pptxgenjs playwright sharp

# System tools (for thumbnails)
brew install libreoffice poppler  # macOS
apt-get install libreoffice poppler-utils  # Ubuntu
```

## Best Practices

1. **Always validate** output immediately after creation
2. **Use web-safe fonts** to ensure cross-platform compatibility
3. **Check overflow** - text that doesn't fit won't render
4. **Batch similar operations** for efficiency
5. **Keep unused resources** out of final file (fonts, media)
6. **Test on target platform** - PowerPoint vs Keynote vs Google Slides
