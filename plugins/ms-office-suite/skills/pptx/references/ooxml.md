# Office Open XML Technical Reference for PowerPoint

**Important**: Read this entire document before editing PowerPoint files. Critical XML schema rules can create invalid PPTX files that won't open.

## Technical Guidelines

### Schema Compliance
- **Element ordering in `<p:txBody>`**: `<a:bodyPr>`, `<a:lstStyle>`, `<a:p>`
- **Whitespace**: Add `xml:space='preserve'` to `<a:t>` elements with leading/trailing spaces
- **Unicode**: Escape characters: `"` becomes `&#8220;`
- **Images**: Add to `ppt/media/`, reference in slide XML, set dimensions
- **Relationships**: Update `ppt/slides/_rels/slideN.xml.rels` for resources
- **Dirty attribute**: Add `dirty="0"` to `<a:rPr>` and `<a:endParaRPr>`

## Presentation Structure

### Basic Slide

```xml
<!-- ppt/slides/slide1.xml -->
<p:sld>
  <p:cSld>
    <p:spTree>
      <p:nvGrpSpPr>...</p:nvGrpSpPr>
      <p:grpSpPr>...</p:grpSpPr>
      <!-- Shapes go here -->
    </p:spTree>
  </p:cSld>
</p:sld>
```

### Text Box / Shape with Text

```xml
<p:sp>
  <p:nvSpPr>
    <p:cNvPr id="2" name="Title"/>
    <p:cNvSpPr>
      <a:spLocks noGrp="1"/>
    </p:cNvSpPr>
    <p:nvPr>
      <p:ph type="ctrTitle"/>
    </p:nvPr>
  </p:nvSpPr>
  <p:spPr>
    <a:xfrm>
      <a:off x="838200" y="365125"/>
      <a:ext cx="7772400" cy="1470025"/>
    </a:xfrm>
  </p:spPr>
  <p:txBody>
    <a:bodyPr/>
    <a:lstStyle/>
    <a:p>
      <a:r>
        <a:t>Slide Title</a:t>
      </a:r>
    </a:p>
  </p:txBody>
</p:sp>
```

### Text Formatting

```xml
<!-- Bold -->
<a:r>
  <a:rPr b="1"/>
  <a:t>Bold Text</a:t>
</a:r>

<!-- Italic -->
<a:r>
  <a:rPr i="1"/>
  <a:t>Italic Text</a:t>
</a:r>

<!-- Font, Size, Color -->
<a:r>
  <a:rPr lang="en-US" sz="2400" b="1" dirty="0">
    <a:solidFill>
      <a:srgbClr val="FF0000"/>
    </a:solidFill>
  </a:rPr>
  <a:t>Formatted text</a:t>
</a:r>

<!-- Highlight -->
<a:r>
  <a:rPr>
    <a:highlight>
      <a:srgbClr val="FFFF00"/>
    </a:highlight>
  </a:rPr>
  <a:t>Highlighted</a:t>
</a:r>
```

### Lists

```xml
<!-- Bullet list -->
<a:p>
  <a:pPr lvl="0">
    <a:buChar char="•"/>
  </a:pPr>
  <a:r>
    <a:t>First bullet point</a:t>
  </a:r>
</a:p>

<!-- Numbered list -->
<a:p>
  <a:pPr lvl="0">
    <a:buAutoNum type="arabicPeriod"/>
  </a:pPr>
  <a:r>
    <a:t>First numbered item</a:t>
  </a:r>
</a:p>
```

### Shapes

```xml
<!-- Rectangle -->
<p:sp>
  <p:nvSpPr>
    <p:cNvPr id="3" name="Rectangle"/>
    <p:cNvSpPr/>
    <p:nvPr/>
  </p:nvSpPr>
  <p:spPr>
    <a:xfrm>
      <a:off x="1000000" y="1000000"/>
      <a:ext cx="3000000" cy="2000000"/>
    </a:xfrm>
    <a:prstGeom prst="rect">
      <a:avLst/>
    </a:prstGeom>
    <a:solidFill>
      <a:srgbClr val="4472C4"/>
    </a:solidFill>
    <a:ln w="25400">
      <a:solidFill>
        <a:srgbClr val="000000"/>
      </a:solidFill>
    </a:ln>
  </p:spPr>
</p:sp>
```

### Images

```xml
<p:pic>
  <p:nvPicPr>
    <p:cNvPr id="4" name="Picture"/>
    <p:cNvPicPr>
      <a:picLocks noChangeAspect="1"/>
    </p:cNvPicPr>
    <p:nvPr/>
  </p:nvPicPr>
  <p:blipFill>
    <a:blip r:embed="rId2"/>
    <a:stretch>
      <a:fillRect/>
    </a:stretch>
  </p:blipFill>
  <p:spPr>
    <a:xfrm>
      <a:off x="1000000" y="1000000"/>
      <a:ext cx="3000000" cy="2000000"/>
    </a:xfrm>
    <a:prstGeom prst="rect">
      <a:avLst/>
    </a:prstGeom>
  </p:spPr>
</p:pic>
```

### Tables

```xml
<p:graphicFrame>
  <p:nvGraphicFramePr>
    <p:cNvPr id="5" name="Table"/>
    <p:cNvGraphicFramePr>
      <a:graphicFrameLocks noGrp="1"/>
    </p:cNvGraphicFramePr>
    <p:nvPr/>
  </p:nvGraphicFramePr>
  <p:xfrm>
    <a:off x="1000000" y="1000000"/>
    <a:ext cx="6000000" cy="2000000"/>
  </p:xfrm>
  <a:graphic>
    <a:graphicData uri="http://schemas.openxmlformats.org/drawingml/2006/table">
      <a:tbl>
        <a:tblGrid>
          <a:gridCol w="3000000"/>
          <a:gridCol w="3000000"/>
        </a:tblGrid>
        <a:tr h="500000">
          <a:tc>
            <a:txBody>
              <a:bodyPr/>
              <a:lstStyle/>
              <a:p><a:r><a:t>Cell 1</a:t></a:r></a:p>
            </a:txBody>
          </a:tc>
          <a:tc>
            <a:txBody>
              <a:bodyPr/>
              <a:lstStyle/>
              <a:p><a:r><a:t>Cell 2</a:t></a:r></a:p>
            </a:txBody>
          </a:tc>
        </a:tr>
      </a:tbl>
    </a:graphicData>
  </a:graphic>
</p:graphicFrame>
```

## File Updates

When adding content, update these files:

### ppt/_rels/presentation.xml.rels

```xml
<Relationship Id="rId1"
    Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/slide"
    Target="slides/slide1.xml"/>
```

### ppt/slides/_rels/slide1.xml.rels

```xml
<Relationship Id="rId1"
    Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/slideLayout"
    Target="../slideLayouts/slideLayout1.xml"/>
<Relationship Id="rId2"
    Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/image"
    Target="../media/image1.png"/>
```

### [Content_Types].xml

```xml
<Default Extension="png" ContentType="image/png"/>
<Override PartName="/ppt/slides/slide1.xml"
    ContentType="application/vnd.openxmlformats-officedocument.presentationml.slide+xml"/>
```

### ppt/presentation.xml

```xml
<p:sldIdLst>
  <p:sldId id="256" r:id="rId1"/>
  <p:sldId id="257" r:id="rId2"/>
</p:sldIdLst>
```

## Slide Operations

### Adding a New Slide

1. Create `ppt/slides/slideN.xml`
2. Update `[Content_Types].xml` with Override
3. Update `ppt/_rels/presentation.xml.rels`
4. Update `ppt/presentation.xml` `<p:sldIdLst>`
5. Create `ppt/slides/_rels/slideN.xml.rels` if needed

### Reordering Slides

Reorder `<p:sldId>` elements in `<p:sldIdLst>`:

```xml
<!-- Original order -->
<p:sldIdLst>
  <p:sldId id="256" r:id="rId2"/>
  <p:sldId id="257" r:id="rId3"/>
  <p:sldId id="258" r:id="rId4"/>
</p:sldIdLst>

<!-- After moving slide 3 to position 2 -->
<p:sldIdLst>
  <p:sldId id="256" r:id="rId2"/>
  <p:sldId id="258" r:id="rId4"/>
  <p:sldId id="257" r:id="rId3"/>
</p:sldIdLst>
```

**Keep slide IDs and relationship IDs unchanged** - only change the order.

### Deleting a Slide

1. Remove `<p:sldId>` from `ppt/presentation.xml`
2. Remove relationship from `ppt/_rels/presentation.xml.rels`
3. Remove Override from `[Content_Types].xml`
4. Delete `ppt/slides/slideN.xml` and `_rels/slideN.xml.rels`
5. Clean up orphaned media in `ppt/media/`

Don't renumber remaining slides.

## Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| Won't open | Invalid XML | Validate structure |
| Missing content | Wrong element order | Check `<p:txBody>` children |
| Broken images | Missing relationship | Update slide `_rels` file |
| Corrupt file | Missing content type | Check `[Content_Types].xml` |
| Slides out of order | Wrong `sldIdLst` order | Reorder `<p:sldId>` elements |

## Validation Checklist

Before saving:
- [ ] Clean unused resources (media, fonts, notes)
- [ ] Declare ALL slides in `[Content_Types].xml`
- [ ] Remove font embed references if not using embedded fonts
- [ ] Check all `_rels` files for broken references

## Common Template Pitfalls

- Multiple slides referencing same notes slide after duplication
- Image references from template slides that no longer exist
- Font embedding references when fonts aren't included
- Missing slideLayout declarations
