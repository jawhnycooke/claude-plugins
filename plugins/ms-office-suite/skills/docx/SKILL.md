---
name: docx
description: Use this skill when working with Word documents (.docx files) - creating, editing, reading, adding tracked changes, comments, or formatting.
---

# Word Document (.docx) Guide

This guide covers creating, editing, and analyzing Word documents. For JavaScript-based creation, see [docx-js.md](references/docx-js.md). For OOXML technical details and tracked changes, see [ooxml.md](references/ooxml.md).

## Workflows Overview

| Task | Approach | Reference |
|------|----------|-----------|
| Read/analyze document | pandoc or raw XML | This file |
| Create new document | docx-js (JavaScript) | docx-js.md |
| Edit existing document | Python Document library | ooxml.md |
| Add tracked changes | Python Document library | ooxml.md |

## Reading & Analysis

### Convert to Markdown (Quick Read)

```bash
pandoc document.docx -o output.md
```

### Extract Text with Python

```python
from docx import Document

doc = Document("document.docx")
for para in doc.paragraphs:
    print(para.text)
```

### Access Raw XML (Detailed Analysis)

```bash
# Unpack the .docx file
unzip document.docx -d unpacked/

# Main document content
cat unpacked/word/document.xml

# Styles
cat unpacked/word/styles.xml

# Comments
cat unpacked/word/comments.xml
```

### Visual Analysis

Convert to PDF then to images for visual inspection:

```bash
# Using LibreOffice
libreoffice --headless --convert-to pdf document.docx

# Convert PDF to images
pdftoppm -png -r 200 document.pdf page
```

## Creating Documents (docx-js)

For new documents, use the docx-js library. **Read [docx-js.md](references/docx-js.md) fully before starting.**

### Basic Example

```javascript
const { Document, Packer, Paragraph, TextRun, HeadingLevel } = require("docx");
const fs = require("fs");

const doc = new Document({
    sections: [{
        properties: {},
        children: [
            new Paragraph({
                text: "Document Title",
                heading: HeadingLevel.HEADING_1
            }),
            new Paragraph({
                children: [
                    new TextRun("Normal text with "),
                    new TextRun({ text: "bold", bold: true }),
                    new TextRun(" formatting.")
                ]
            })
        ]
    }]
});

Packer.toBuffer(doc).then(buffer => {
    fs.writeFileSync("output.docx", buffer);
});
```

## Editing Documents

For editing existing documents, use the Python approach with direct XML manipulation.

### Simple Text Replacement

```python
from docx import Document

doc = Document("template.docx")

for para in doc.paragraphs:
    if "{{NAME}}" in para.text:
        para.text = para.text.replace("{{NAME}}", "John Doe")

doc.save("filled.docx")
```

### Advanced Editing (Preserve Formatting)

For edits that preserve formatting, work with XML directly:

```python
# Unpack, modify XML, repack
import zipfile
import xml.etree.ElementTree as ET

# Extract
with zipfile.ZipFile("document.docx", "r") as zip_ref:
    zip_ref.extractall("unpacked")

# Parse and modify
tree = ET.parse("unpacked/word/document.xml")
root = tree.getroot()
# ... make changes ...
tree.write("unpacked/word/document.xml", xml_declaration=True)

# Repack
with zipfile.ZipFile("modified.docx", "w") as zipf:
    for root, dirs, files in os.walk("unpacked"):
        for file in files:
            file_path = os.path.join(root, file)
            arcname = os.path.relpath(file_path, "unpacked")
            zipf.write(file_path, arcname)
```

## Tracked Changes (Redlining)

For professional document editing with tracked changes:

1. **Convert to markdown** to understand content
2. **Identify changes** in batches of 3-10 edits
3. **Apply to XML** preserving original run attributes

**Critical guideline**: Only mark text that actually changes. Repeating unchanged text makes edits harder to review.

See [ooxml.md](references/ooxml.md) for detailed tracked changes implementation.

### Tracked Change Example

```xml
<!-- Original: "The quick brown fox" -->
<!-- After deletion of "quick ": -->
<w:p>
  <w:r>
    <w:t>The </w:t>
  </w:r>
  <w:del w:author="Editor" w:date="2024-01-15T10:30:00Z">
    <w:r>
      <w:delText>quick </w:delText>
    </w:r>
  </w:del>
  <w:r>
    <w:t>brown fox</w:t>
  </w:r>
</w:p>
```

## Adding Comments

```python
from docx import Document
from docx.oxml.ns import qn
from docx.oxml import OxmlElement

doc = Document("document.docx")

# Add comment to first paragraph
para = doc.paragraphs[0]
comment = OxmlElement("w:commentReference")
comment.set(qn("w:id"), "1")
para._p.append(comment)

# Add comment content to comments.xml
# (requires direct XML manipulation)

doc.save("commented.docx")
```

## Document Properties

```python
from docx import Document

doc = Document("document.docx")

# Read properties
core_props = doc.core_properties
print(f"Title: {core_props.title}")
print(f"Author: {core_props.author}")
print(f"Created: {core_props.created}")

# Set properties
core_props.title = "New Title"
core_props.author = "New Author"

doc.save("updated.docx")
```

## Dependencies

```bash
# Python
pip install python-docx lxml

# JavaScript
npm install docx

# CLI tools
brew install pandoc libreoffice  # macOS
apt-get install pandoc libreoffice  # Ubuntu
```

## Best Practices

1. **Always read reference files** (docx-js.md and ooxml.md) before complex operations
2. **Batch edits logically** - group related changes together
3. **Preserve formatting** - work at the run level, not paragraph level
4. **Validate output** - open in Word to verify no corruption
5. **Keep backups** - OOXML manipulation can corrupt files if done incorrectly
