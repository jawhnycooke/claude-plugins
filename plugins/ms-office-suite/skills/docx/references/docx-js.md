# docx-js Reference Guide

Complete guide to generating Word documents (.docx) with JavaScript/TypeScript using the `docx` library.

## Setup

```javascript
const {
    Document, Packer, Paragraph, TextRun, HeadingLevel,
    Table, TableRow, TableCell, WidthType,
    ImageRun, Header, Footer, PageNumber,
    NumberFormat, AlignmentType, BorderStyle,
    ShadingType, TableOfContents, ExternalHyperlink
} = require("docx");
const fs = require("fs");
```

## Core Concepts

- **Document**: Top-level container with sections
- **Section**: Contains paragraphs, tables, headers/footers
- **Paragraph**: Contains text runs, can have styling
- **TextRun**: Actual text content with formatting

## Text & Formatting

### Basic Text

```javascript
new Paragraph({
    children: [
        new TextRun("Plain text"),
        new TextRun({ text: " bold", bold: true }),
        new TextRun({ text: " italic", italics: true }),
        new TextRun({ text: " underline", underline: {} }),
    ]
})
```

### Colors and Highlighting

```javascript
new TextRun({
    text: "Colored text",
    color: "FF0000",  // Red (no # prefix)
    bold: true
})

new TextRun({
    text: "Highlighted",
    highlight: "yellow",
    shading: {
        type: ShadingType.CLEAR,  // CRITICAL: Always use CLEAR
        fill: "FFFF00"
    }
})
```

### Font and Size

```javascript
new TextRun({
    text: "Custom font",
    font: "Arial",
    size: 28  // Size in half-points (28 = 14pt)
})
```

## Headings

```javascript
new Paragraph({
    text: "Heading 1",
    heading: HeadingLevel.HEADING_1
})

new Paragraph({
    text: "Heading 2",
    heading: HeadingLevel.HEADING_2
})
```

## Lists

**CRITICAL**: Always use numbering config, NOT unicode symbols (•, -, etc.)

### Bullet Lists

```javascript
const doc = new Document({
    numbering: {
        config: [{
            reference: "bullet-list",
            levels: [{
                level: 0,
                format: NumberFormat.BULLET,
                text: "•",
                alignment: AlignmentType.LEFT,
                style: { paragraph: { indent: { left: 720, hanging: 360 } } }
            }]
        }]
    },
    sections: [{
        children: [
            new Paragraph({
                text: "First item",
                numbering: { reference: "bullet-list", level: 0 }
            }),
            new Paragraph({
                text: "Second item",
                numbering: { reference: "bullet-list", level: 0 }
            })
        ]
    }]
});
```

### Numbered Lists

```javascript
const doc = new Document({
    numbering: {
        config: [{
            reference: "numbered-list",
            levels: [{
                level: 0,
                format: NumberFormat.DECIMAL,
                text: "%1.",
                alignment: AlignmentType.LEFT,
                style: { paragraph: { indent: { left: 720, hanging: 360 } } }
            }]
        }]
    },
    sections: [{
        children: [
            new Paragraph({
                text: "Step one",
                numbering: { reference: "numbered-list", level: 0 }
            }),
            new Paragraph({
                text: "Step two",
                numbering: { reference: "numbered-list", level: 0 }
            })
        ]
    }]
});
```

## Tables

**CRITICAL**: Always specify columnWidths and cell widths

```javascript
new Table({
    columnWidths: [3000, 3000, 3000],  // EMUs (914400 = 1 inch)
    rows: [
        new TableRow({
            children: [
                new TableCell({
                    width: { size: 3000, type: WidthType.DXA },
                    children: [new Paragraph("Header 1")],
                    shading: { fill: "CCCCCC" }
                }),
                new TableCell({
                    width: { size: 3000, type: WidthType.DXA },
                    children: [new Paragraph("Header 2")],
                    shading: { fill: "CCCCCC" }
                }),
                new TableCell({
                    width: { size: 3000, type: WidthType.DXA },
                    children: [new Paragraph("Header 3")],
                    shading: { fill: "CCCCCC" }
                })
            ]
        }),
        new TableRow({
            children: [
                new TableCell({
                    width: { size: 3000, type: WidthType.DXA },
                    children: [new Paragraph("Cell 1")]
                }),
                new TableCell({
                    width: { size: 3000, type: WidthType.DXA },
                    children: [new Paragraph("Cell 2")]
                }),
                new TableCell({
                    width: { size: 3000, type: WidthType.DXA },
                    children: [new Paragraph("Cell 3")]
                })
            ]
        })
    ]
})
```

### Table Borders

```javascript
new Table({
    borders: {
        top: { style: BorderStyle.SINGLE, size: 1, color: "000000" },
        bottom: { style: BorderStyle.SINGLE, size: 1, color: "000000" },
        left: { style: BorderStyle.SINGLE, size: 1, color: "000000" },
        right: { style: BorderStyle.SINGLE, size: 1, color: "000000" },
        insideHorizontal: { style: BorderStyle.SINGLE, size: 1, color: "CCCCCC" },
        insideVertical: { style: BorderStyle.SINGLE, size: 1, color: "CCCCCC" }
    },
    // ... rows
})
```

## Images

**CRITICAL**: Always specify `type` parameter

```javascript
const imageBuffer = fs.readFileSync("image.png");

new Paragraph({
    children: [
        new ImageRun({
            data: imageBuffer,
            type: "png",  // REQUIRED: "png", "jpg", "gif", etc.
            transformation: {
                width: 200,  // points
                height: 150
            }
        })
    ]
})
```

## Hyperlinks

```javascript
new Paragraph({
    children: [
        new TextRun("Visit "),
        new ExternalHyperlink({
            children: [
                new TextRun({
                    text: "our website",
                    style: "Hyperlink"
                })
            ],
            link: "https://example.com"
        })
    ]
})
```

## Table of Contents

```javascript
new TableOfContents("Table of Contents", {
    hyperlink: true,
    headingStyleRange: "1-3"  // Include H1, H2, H3
})
```

## Headers and Footers

```javascript
const doc = new Document({
    sections: [{
        headers: {
            default: new Header({
                children: [
                    new Paragraph({
                        children: [
                            new TextRun("Document Title"),
                        ],
                        alignment: AlignmentType.CENTER
                    })
                ]
            })
        },
        footers: {
            default: new Footer({
                children: [
                    new Paragraph({
                        children: [
                            new TextRun("Page "),
                            new TextRun({
                                children: [PageNumber.CURRENT]
                            }),
                            new TextRun(" of "),
                            new TextRun({
                                children: [PageNumber.TOTAL_PAGES]
                            })
                        ],
                        alignment: AlignmentType.CENTER
                    })
                ]
            })
        },
        children: [/* content */]
    }]
});
```

## Page Layout

```javascript
const doc = new Document({
    sections: [{
        properties: {
            page: {
                size: {
                    width: 12240,  // 8.5 inches in twips
                    height: 15840  // 11 inches in twips
                },
                margin: {
                    top: 1440,     // 1 inch
                    right: 1440,
                    bottom: 1440,
                    left: 1440
                },
                orientation: "portrait"  // or "landscape"
            }
        },
        children: [/* content */]
    }]
});
```

## Page Breaks

**CRITICAL**: PageBreak must be inside Paragraph

```javascript
// CORRECT
new Paragraph({
    children: [new PageBreak()]
})

// WRONG - will fail
new PageBreak()  // Not wrapped in Paragraph
```

## Line Breaks

**CRITICAL**: Use Break class, not "\n"

```javascript
const { Break } = require("docx");

new Paragraph({
    children: [
        new TextRun("Line 1"),
        new Break(),
        new TextRun("Line 2")
    ]
})
```

## Saving Documents

```javascript
// To file
Packer.toBuffer(doc).then(buffer => {
    fs.writeFileSync("document.docx", buffer);
});

// To base64
Packer.toBase64String(doc).then(base64 => {
    console.log(base64);
});
```

## Common Mistakes

| Mistake | Correct Approach |
|---------|-----------------|
| Using "\n" for line breaks | Use `new Break()` |
| Missing `ShadingType.CLEAR` | Always use `type: ShadingType.CLEAR` |
| PageBreak outside Paragraph | Wrap in `new Paragraph({ children: [new PageBreak()] })` |
| Missing image `type` | Always specify: `type: "png"` |
| Unicode bullets in lists | Use numbering config |
| Missing columnWidths in tables | Always specify `columnWidths` array |
| Colors with # prefix | Use without #: `"FF0000"` not `"#FF0000"` |

## Complete Example

```javascript
const {
    Document, Packer, Paragraph, TextRun, HeadingLevel,
    Table, TableRow, TableCell, WidthType, Break,
    AlignmentType, PageBreak, NumberFormat
} = require("docx");
const fs = require("fs");

const doc = new Document({
    numbering: {
        config: [{
            reference: "bullets",
            levels: [{
                level: 0,
                format: NumberFormat.BULLET,
                text: "•",
                alignment: AlignmentType.LEFT,
                style: { paragraph: { indent: { left: 720, hanging: 360 } } }
            }]
        }]
    },
    sections: [{
        children: [
            new Paragraph({
                text: "Project Report",
                heading: HeadingLevel.HEADING_1
            }),
            new Paragraph({
                children: [
                    new TextRun("This report covers "),
                    new TextRun({ text: "important findings", bold: true }),
                    new TextRun(" from our analysis.")
                ]
            }),
            new Paragraph({
                text: "Key Points",
                heading: HeadingLevel.HEADING_2
            }),
            new Paragraph({
                text: "First key point",
                numbering: { reference: "bullets", level: 0 }
            }),
            new Paragraph({
                text: "Second key point",
                numbering: { reference: "bullets", level: 0 }
            }),
            new Paragraph({ children: [new PageBreak()] }),
            new Paragraph({
                text: "Data Summary",
                heading: HeadingLevel.HEADING_2
            }),
            new Table({
                columnWidths: [4000, 4000],
                rows: [
                    new TableRow({
                        children: [
                            new TableCell({
                                width: { size: 4000, type: WidthType.DXA },
                                children: [new Paragraph("Metric")]
                            }),
                            new TableCell({
                                width: { size: 4000, type: WidthType.DXA },
                                children: [new Paragraph("Value")]
                            })
                        ]
                    }),
                    new TableRow({
                        children: [
                            new TableCell({
                                width: { size: 4000, type: WidthType.DXA },
                                children: [new Paragraph("Revenue")]
                            }),
                            new TableCell({
                                width: { size: 4000, type: WidthType.DXA },
                                children: [new Paragraph("$1,000,000")]
                            })
                        ]
                    })
                ]
            })
        ]
    }]
});

Packer.toBuffer(doc).then(buffer => {
    fs.writeFileSync("report.docx", buffer);
    console.log("Document created successfully!");
});
```
