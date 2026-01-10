# Office Open XML Technical Reference for Word

**Important**: Read this entire document before editing Word documents. Critical XML schema rules and formatting requirements are covered throughout.

## Technical Guidelines

### Schema Compliance
- **Element ordering**: Children of `<w:r>` must be: `<w:rPr>`, then content (`<w:t>`, `<w:tab>`, etc.)
- **Whitespace**: Add `xml:space="preserve"` to `<w:t>` elements with leading/trailing spaces
- **Unicode**: Escape characters in ASCII content: `"` becomes `&#8220;`

### Document Structure

```
document.docx (ZIP archive)
├── [Content_Types].xml
├── _rels/
│   └── .rels
├── word/
│   ├── document.xml      # Main content
│   ├── styles.xml        # Style definitions
│   ├── comments.xml      # Comments (if any)
│   ├── settings.xml      # Document settings
│   └── _rels/
│       └── document.xml.rels
└── docProps/
    ├── app.xml
    └── core.xml
```

## Document Content Patterns

### Basic Paragraph

```xml
<w:p>
  <w:pPr>
    <w:pStyle w:val="Normal"/>
  </w:pPr>
  <w:r>
    <w:t>Hello, World!</w:t>
  </w:r>
</w:p>
```

### Formatted Text

```xml
<w:p>
  <w:r>
    <w:rPr>
      <w:b/>                          <!-- Bold -->
      <w:i/>                          <!-- Italic -->
      <w:u w:val="single"/>           <!-- Underline -->
      <w:color w:val="FF0000"/>       <!-- Red color -->
      <w:sz w:val="28"/>              <!-- 14pt (half-points) -->
      <w:rFonts w:ascii="Arial"/>     <!-- Font -->
    </w:rPr>
    <w:t>Formatted text</w:t>
  </w:r>
</w:p>
```

### Headings

```xml
<w:p>
  <w:pPr>
    <w:pStyle w:val="Heading1"/>
  </w:pPr>
  <w:r>
    <w:t>Chapter Title</w:t>
  </w:r>
</w:p>
```

### Lists

```xml
<!-- Bullet list item -->
<w:p>
  <w:pPr>
    <w:pStyle w:val="ListParagraph"/>
    <w:numPr>
      <w:ilvl w:val="0"/>
      <w:numId w:val="1"/>
    </w:numPr>
  </w:pPr>
  <w:r>
    <w:t>List item</w:t>
  </w:r>
</w:p>
```

### Tables

```xml
<w:tbl>
  <w:tblPr>
    <w:tblStyle w:val="TableGrid"/>
    <w:tblW w:w="0" w:type="auto"/>
  </w:tblPr>
  <w:tblGrid>
    <w:gridCol w:w="4000"/>
    <w:gridCol w:w="4000"/>
  </w:tblGrid>
  <w:tr>
    <w:tc>
      <w:tcPr>
        <w:tcW w:w="4000" w:type="dxa"/>
      </w:tcPr>
      <w:p>
        <w:r>
          <w:t>Cell 1</w:t>
        </w:r>
      </w:p>
    </w:tc>
    <w:tc>
      <w:tcPr>
        <w:tcW w:w="4000" w:type="dxa"/>
      </w:tcPr>
      <w:p>
        <w:r>
          <w:t>Cell 2</w:t>
        </w:r>
      </w:p>
    </w:tc>
  </w:tr>
</w:tbl>
```

## Tracked Changes

### Deletion

```xml
<w:p>
  <w:r>
    <w:t>The </w:t>
  </w:r>
  <w:del w:id="1" w:author="Editor" w:date="2024-01-15T10:30:00Z">
    <w:r>
      <w:rPr>
        <!-- Preserve original formatting -->
      </w:rPr>
      <w:delText>removed </w:delText>
    </w:r>
  </w:del>
  <w:r>
    <w:t>text</w:t>
  </w:r>
</w:p>
```

### Insertion

```xml
<w:p>
  <w:r>
    <w:t>Original </w:t>
  </w:r>
  <w:ins w:id="2" w:author="Editor" w:date="2024-01-15T10:30:00Z">
    <w:r>
      <w:t>new </w:t>
    </w:r>
  </w:ins>
  <w:r>
    <w:t>text</w:t>
  </w:r>
</w:p>
```

### Critical Guidelines for Tracked Changes

1. **Never modify text within existing tracked changes** - Word treats these as atomic
2. **Placement rules**:
   - `<w:del>` wraps the run being deleted
   - `<w:ins>` wraps new runs being added
3. **Preserve run properties** - Copy `<w:rPr>` from original run
4. **Use unique IDs** - Each tracked change needs a unique `w:id`
5. **Consistent author/date** - Use same values throughout document

### Redlining Workflow

1. **Read document** - Understand structure before editing
2. **Identify changes** - Group into batches of 3-10 related edits
3. **Apply minimally** - Only mark text that actually changes
4. **Preserve formatting** - Copy original `<w:rPr>` elements
5. **Validate** - Open in Word to verify changes display correctly

## Comments

### Comments.xml Structure

```xml
<?xml version="1.0" encoding="UTF-8"?>
<w:comments xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main">
  <w:comment w:id="1" w:author="Reviewer" w:date="2024-01-15T10:00:00Z">
    <w:p>
      <w:r>
        <w:t>This needs clarification.</w:t>
      </w:r>
    </w:p>
  </w:comment>
</w:comments>
```

### Comment Reference in Document

```xml
<w:p>
  <w:commentRangeStart w:id="1"/>
  <w:r>
    <w:t>Commented text</w:t>
  </w:r>
  <w:commentRangeEnd w:id="1"/>
  <w:r>
    <w:rPr>
      <w:rStyle w:val="CommentReference"/>
    </w:rPr>
    <w:commentReference w:id="1"/>
  </w:r>
</w:p>
```

## File Updates

When modifying documents, update these files:

### [Content_Types].xml

```xml
<Override PartName="/word/comments.xml"
    ContentType="application/vnd.openxmlformats-officedocument.wordprocessingml.comments+xml"/>
```

### word/_rels/document.xml.rels

```xml
<Relationship Id="rId4" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/comments" Target="comments.xml"/>
```

## Python Document Library

For programmatic editing with tracked changes:

```python
from scripts.document import Document

# Load document
doc = Document("input.docx")

# Add tracked change (deletion)
doc.suggest_deletion(
    line_number=15,
    original_text="old text",
    author="Editor"
)

# Add tracked change (replacement)
doc.replace_node(
    line_number=20,
    old_text="original",
    new_text="replacement",
    author="Editor"
)

# Save
doc.save("output.docx")
```

### Document Class Methods

| Method | Description |
|--------|-------------|
| `suggest_deletion(line, text, author)` | Mark text for deletion |
| `replace_node(line, old, new, author)` | Replace text with tracking |
| `add_comment(line, text, comment, author)` | Add comment to text |
| `revert_change(change_id)` | Revert a tracked change |

## Validation

Before distributing edited documents:

1. **Open in Word** - Verify no corruption
2. **Review tracked changes** - Ensure they display correctly
3. **Check comments** - Verify proper linking
4. **Test print preview** - Confirm layout integrity

## Namespace Reference

Common namespaces in OOXML:

```xml
xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main"
xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships"
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
xmlns:w14="http://schemas.microsoft.com/office/word/2010/wordml"
```

## Common Errors to Avoid

| Error | Cause | Solution |
|-------|-------|----------|
| Document won't open | Invalid XML | Validate XML structure |
| Tracked changes missing | Wrong element nesting | Check `<w:del>`/`<w:ins>` placement |
| Formatting lost | Missing `<w:rPr>` | Copy from original run |
| Comments not showing | Missing relationship | Update document.xml.rels |
| Corrupted file | Wrong content type | Check [Content_Types].xml |
