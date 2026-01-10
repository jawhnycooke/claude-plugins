---
name: pdf
description: Use this skill when working with PDF files - reading, creating, editing, merging, splitting, extracting text/tables, filling forms, or processing scanned documents with OCR.
---

# PDF Processing Guide

This guide covers essential PDF operations using Python libraries and command-line tools. For advanced features and JavaScript libraries, see [reference.md](references/reference.md). For PDF form completion, see [forms.md](references/forms.md).

## Quick Reference

| Task | Tool | Command/Method |
|------|------|----------------|
| Read text | pdfplumber | `page.extract_text()` |
| Extract tables | pdfplumber | `page.extract_tables()` |
| Merge PDFs | pypdf | `PdfMerger().append()` |
| Split pages | pypdf | `PdfWriter().add_page()` |
| Create PDF | reportlab | `canvas.Canvas()` |
| Text extraction (CLI) | pdftotext | `pdftotext -layout file.pdf` |
| Merge/split (CLI) | qpdf | `qpdf --pages` |
| OCR scanned docs | pytesseract | `image_to_string()` |

## Reading PDFs

### Extract Text with pdfplumber

```python
import pdfplumber

with pdfplumber.open("document.pdf") as pdf:
    for page in pdf.pages:
        text = page.extract_text()
        print(text)
```

### Extract Tables to Excel

```python
import pdfplumber
import pandas as pd

with pdfplumber.open("report.pdf") as pdf:
    tables = []
    for page in pdf.pages:
        page_tables = page.extract_tables()
        for table in page_tables:
            df = pd.DataFrame(table[1:], columns=table[0])
            tables.append(df)

    # Export to Excel
    with pd.ExcelWriter("output.xlsx") as writer:
        for i, df in enumerate(tables):
            df.to_excel(writer, sheet_name=f"Table_{i+1}", index=False)
```

### Get PDF Metadata

```python
from pypdf import PdfReader

reader = PdfReader("document.pdf")
info = reader.metadata
print(f"Title: {info.title}")
print(f"Author: {info.author}")
print(f"Pages: {len(reader.pages)}")
```

## Creating PDFs

### Simple PDF with reportlab

```python
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas

c = canvas.Canvas("output.pdf", pagesize=letter)
c.setFont("Helvetica", 12)
c.drawString(100, 750, "Hello, World!")
c.save()
```

### Multi-page Document with Platypus

```python
from reportlab.lib.pagesizes import letter
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer
from reportlab.lib.styles import getSampleStyleSheet

doc = SimpleDocTemplate("report.pdf", pagesize=letter)
styles = getSampleStyleSheet()
story = []

story.append(Paragraph("Report Title", styles['Heading1']))
story.append(Spacer(1, 12))
story.append(Paragraph("This is the body text.", styles['Normal']))

doc.build(story)
```

## Merging and Splitting

### Merge Multiple PDFs

```python
from pypdf import PdfMerger

merger = PdfMerger()
merger.append("file1.pdf")
merger.append("file2.pdf")
merger.append("file3.pdf")
merger.write("merged.pdf")
merger.close()
```

### Split PDF into Pages

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("document.pdf")
for i, page in enumerate(reader.pages):
    writer = PdfWriter()
    writer.add_page(page)
    writer.write(f"page_{i+1}.pdf")
```

### Extract Page Range

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("document.pdf")
writer = PdfWriter()

# Extract pages 5-10 (0-indexed)
for page_num in range(4, 10):
    writer.add_page(reader.pages[page_num])

writer.write("extracted.pdf")
```

## Command-Line Tools

### pdftotext (from poppler-utils)

```bash
# Extract text with layout preservation
pdftotext -layout document.pdf output.txt

# Extract specific pages
pdftotext -f 1 -l 5 document.pdf output.txt

# Output to stdout
pdftotext document.pdf -
```

### qpdf

```bash
# Merge PDFs
qpdf --empty --pages file1.pdf file2.pdf -- merged.pdf

# Extract pages 1-5
qpdf document.pdf --pages . 1-5 -- extracted.pdf

# Rotate pages
qpdf document.pdf --rotate=90:1-5 rotated.pdf

# Remove password
qpdf --password=secret --decrypt protected.pdf unlocked.pdf
```

### pdftk (alternative)

```bash
# Merge
pdftk file1.pdf file2.pdf cat output merged.pdf

# Split
pdftk document.pdf burst output page_%d.pdf

# Rotate
pdftk document.pdf cat 1-5east output rotated.pdf
```

## Advanced Operations

### OCR Scanned Documents

```python
from pdf2image import convert_from_path
import pytesseract

# Convert PDF pages to images
images = convert_from_path("scanned.pdf", dpi=300)

# OCR each page
text = ""
for image in images:
    text += pytesseract.image_to_string(image)

print(text)
```

### Add Watermark

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("document.pdf")
watermark = PdfReader("watermark.pdf")

writer = PdfWriter()
for page in reader.pages:
    page.merge_page(watermark.pages[0])
    writer.add_page(page)

writer.write("watermarked.pdf")
```

### Extract Images

```bash
# Using pdfimages (from poppler-utils)
pdfimages -png document.pdf images/
```

### Password Protection

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("document.pdf")
writer = PdfWriter()

for page in reader.pages:
    writer.add_page(page)

writer.encrypt("userpassword", "ownerpassword")
writer.write("protected.pdf")
```

## Dependencies

Install required packages:

```bash
# Python libraries
pip install pypdf pdfplumber reportlab pdf2image pytesseract pandas openpyxl

# System tools (macOS)
brew install poppler tesseract qpdf

# System tools (Ubuntu/Debian)
apt-get install poppler-utils tesseract-ocr qpdf
```
