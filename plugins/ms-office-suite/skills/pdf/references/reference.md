# PDF Advanced Reference

Advanced PDF operations using Python and JavaScript libraries.

## pypdfium2 - Fast PDF Rendering

High-performance PDF rendering using PDFium (Chrome's PDF engine).

### Page to Image Conversion

```python
import pypdfium2 as pdfium

pdf = pdfium.PdfDocument("document.pdf")

for i, page in enumerate(pdf):
    # Render at 2x scale (144 DPI)
    bitmap = page.render(scale=2)
    pil_image = bitmap.to_pil()
    pil_image.save(f"page_{i+1}.png")

pdf.close()
```

### Text Extraction with Position

```python
import pypdfium2 as pdfium

pdf = pdfium.PdfDocument("document.pdf")
page = pdf[0]

textpage = page.get_textpage()
text = textpage.get_text_range()

# Get character positions
for i in range(textpage.count_chars()):
    char = textpage.get_text_range(i, 1)
    rect = textpage.get_charbox(i)
    print(f"'{char}' at {rect}")
```

## pdf-lib (JavaScript)

Modern JavaScript library for creating and modifying PDFs.

### Create PDF

```javascript
const { PDFDocument, rgb, StandardFonts } = require('pdf-lib');
const fs = require('fs');

async function createPdf() {
    const pdfDoc = await PDFDocument.create();
    const page = pdfDoc.addPage([612, 792]); // Letter size

    const font = await pdfDoc.embedFont(StandardFonts.Helvetica);

    page.drawText('Hello, World!', {
        x: 50,
        y: 700,
        size: 24,
        font: font,
        color: rgb(0, 0, 0)
    });

    const pdfBytes = await pdfDoc.save();
    fs.writeFileSync('output.pdf', pdfBytes);
}

createPdf();
```

### Modify Existing PDF

```javascript
const { PDFDocument } = require('pdf-lib');
const fs = require('fs');

async function modifyPdf() {
    const existingPdfBytes = fs.readFileSync('input.pdf');
    const pdfDoc = await PDFDocument.load(existingPdfBytes);

    const pages = pdfDoc.getPages();
    const firstPage = pages[0];

    firstPage.drawText('CONFIDENTIAL', {
        x: 50,
        y: 50,
        size: 20,
        color: rgb(1, 0, 0)
    });

    const pdfBytes = await pdfDoc.save();
    fs.writeFileSync('modified.pdf', pdfBytes);
}

modifyPdf();
```

### Copy Pages Between PDFs

```javascript
const { PDFDocument } = require('pdf-lib');
const fs = require('fs');

async function copyPages() {
    const srcDoc = await PDFDocument.load(fs.readFileSync('source.pdf'));
    const destDoc = await PDFDocument.create();

    // Copy pages 0 and 2
    const [page0, page2] = await destDoc.copyPages(srcDoc, [0, 2]);
    destDoc.addPage(page0);
    destDoc.addPage(page2);

    const pdfBytes = await destDoc.save();
    fs.writeFileSync('extracted.pdf', pdfBytes);
}

copyPages();
```

## pdfjs-dist (Mozilla)

Browser-compatible PDF rendering.

### Render to Canvas

```javascript
const pdfjsLib = require('pdfjs-dist/legacy/build/pdf.js');

async function renderPage(pdfPath, pageNum) {
    const loadingTask = pdfjsLib.getDocument(pdfPath);
    const pdf = await loadingTask.promise;
    const page = await pdf.getPage(pageNum);

    const scale = 1.5;
    const viewport = page.getViewport({ scale });

    // In Node.js, use node-canvas
    const { createCanvas } = require('canvas');
    const canvas = createCanvas(viewport.width, viewport.height);
    const context = canvas.getContext('2d');

    await page.render({
        canvasContext: context,
        viewport: viewport
    }).promise;

    return canvas.toBuffer('image/png');
}
```

## poppler-utils Reference

### pdftotext Options

```bash
# Layout-preserving extraction
pdftotext -layout document.pdf output.txt

# Raw text (no layout)
pdftotext -raw document.pdf output.txt

# HTML output
pdftotext -htmlmeta document.pdf output.html

# Page range
pdftotext -f 1 -l 10 document.pdf output.txt

# Specific resolution
pdftotext -r 300 document.pdf output.txt
```

### pdfinfo

```bash
# Get PDF metadata
pdfinfo document.pdf

# Output:
# Title:          My Document
# Author:         John Doe
# Creator:        Microsoft Word
# Pages:          15
# Page size:      612 x 792 pts (letter)
```

### pdfimages

```bash
# Extract all images as PNG
pdfimages -png document.pdf images/

# Extract as JPEG
pdfimages -j document.pdf images/

# List images without extracting
pdfimages -list document.pdf
```

### pdftoppm

```bash
# Convert to PNG at 300 DPI
pdftoppm -png -r 300 document.pdf output

# Convert specific pages
pdftoppm -f 1 -l 5 -png document.pdf output

# Convert to JPEG
pdftoppm -jpeg -jpegopt quality=90 document.pdf output
```

## qpdf Advanced Usage

### Page Manipulation

```bash
# Select specific pages
qpdf input.pdf --pages . 1-3,7,9-12 -- output.pdf

# Reverse page order
qpdf input.pdf --pages . z-1 -- reversed.pdf

# Interleave from two PDFs
qpdf --empty --pages odd.pdf even.pdf -- interleaved.pdf
```

### Encryption

```bash
# Encrypt with AES-256
qpdf --encrypt user-pass owner-pass 256 -- input.pdf encrypted.pdf

# Set permissions
qpdf --encrypt "" owner 256 \
    --print=none --modify=none --extract=n \
    -- input.pdf restricted.pdf
```

### Linearization (Web Optimization)

```bash
# Linearize for fast web viewing
qpdf --linearize input.pdf optimized.pdf
```

## Performance Tips

### Large File Processing

```python
# Process pages individually to reduce memory
from pypdf import PdfReader

reader = PdfReader("large.pdf")
for page_num in range(len(reader.pages)):
    page = reader.pages[page_num]
    text = page.extract_text()
    process_text(text)
    # Page is garbage collected after each iteration
```

### Batch Processing

```python
import concurrent.futures
from pathlib import Path

def process_pdf(pdf_path):
    # Process single PDF
    pass

pdf_files = list(Path("pdfs/").glob("*.pdf"))

with concurrent.futures.ProcessPoolExecutor(max_workers=4) as executor:
    results = list(executor.map(process_pdf, pdf_files))
```

### Memory-Efficient Table Extraction

```python
import pdfplumber

# Stream process large PDFs
with pdfplumber.open("large.pdf") as pdf:
    for page in pdf.pages:
        tables = page.extract_tables()
        # Process and discard immediately
        for table in tables:
            yield table
        # Memory freed after each page
```
