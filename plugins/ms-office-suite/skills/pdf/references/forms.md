# PDF Form Filling Guide

This guide covers two workflows for filling PDF forms: fillable (interactive) forms and non-fillable (static) forms.

## Determine Form Type

First, check if the PDF has fillable fields:

```bash
python scripts/check_fillable_fields.py document.pdf
```

Output will indicate:
- **Fillable**: Has interactive form fields
- **Non-fillable**: Static PDF requiring annotation-based filling

## Workflow 1: Fillable Forms

### Step 1: Extract Field Information

```bash
python scripts/extract_form_field_info.py input.pdf field_info.json
```

This creates a JSON catalog of all form fields with:
- Field name (hierarchical ID)
- Field type (text, checkbox, choice, radio)
- Location and dimensions
- Current value (if any)

### Step 2: Visual Analysis (Optional)

```bash
python scripts/convert_pdf_to_images.py input.pdf output_dir/
```

Creates PNG images at 200 DPI for visual reference.

### Step 3: Create Field Values

Create `field_values.json` mapping field names to values:

```json
{
  "form1[0].#subform[0].FirstName[0]": "John",
  "form1[0].#subform[0].LastName[0]": "Doe",
  "form1[0].#subform[0].DateOfBirth[0]": "1990-01-15",
  "form1[0].#subform[0].Gender[0]": "Male",
  "form1[0].#subform[0].Agree[0]": true
}
```

**Field value formats:**
- **Text fields**: String value
- **Checkboxes**: `true` or `false`
- **Choice/dropdown**: Text of the option to select
- **Radio buttons**: Text of the option to select

### Step 4: Fill the Form

```bash
python scripts/fill_fillable_fields.py input.pdf field_values.json output.pdf
```

## Workflow 2: Non-Fillable Forms

For PDFs without interactive fields, we add text annotations at specific coordinates.

### Step 1: Convert to Images

```bash
python scripts/convert_pdf_to_images.py input.pdf output_dir/
```

### Step 2: Identify Entry Areas

Visually analyze the images to identify:
- Field locations (x, y coordinates)
- Field dimensions (width, height)
- Font size requirements

### Step 3: Create Field Definitions

Create `fields.json` defining each entry area:

```json
{
  "page_1": {
    "first_name": {
      "x": 150,
      "y": 200,
      "width": 200,
      "height": 20,
      "font_size": 12
    },
    "last_name": {
      "x": 150,
      "y": 230,
      "width": 200,
      "height": 20,
      "font_size": 12
    },
    "address": {
      "x": 150,
      "y": 260,
      "width": 400,
      "height": 40,
      "font_size": 10
    }
  }
}
```

**Coordinate system:**
- Origin (0, 0) is at **top-left** of the image
- X increases rightward
- Y increases downward
- Units are pixels at the rendered DPI

### Step 4: Validate Bounding Boxes

```bash
python scripts/check_bounding_boxes.py fields.json
```

Checks for:
- Overlapping fields
- Fields extending beyond page bounds
- Font sizes that won't fit in the defined area

### Step 5: Create Validation Image

```bash
python scripts/create_validation_image.py input.pdf fields.json validation/
```

Creates annotated images showing:
- Red rectangles for entry boxes
- Blue rectangles for labels
- Helps verify correct positioning

### Step 6: Create Field Values

```json
{
  "page_1": {
    "first_name": "John",
    "last_name": "Doe",
    "address": "123 Main Street\nAnytown, USA 12345"
  }
}
```

### Step 7: Fill the Form

```bash
python scripts/fill_pdf_form_with_annotations.py input.pdf fields.json field_values.json output.pdf
```

## Coordinate Transformation

When converting between image coordinates and PDF coordinates:

```python
# Image to PDF coordinate transformation
# PDF origin is at bottom-left, image origin is at top-left

def image_to_pdf_coords(img_x, img_y, img_height, dpi=200):
    """Convert image coordinates to PDF coordinates."""
    scale = 72 / dpi  # PDF points per pixel
    pdf_x = img_x * scale
    pdf_y = (img_height - img_y) * scale  # Flip Y axis
    return pdf_x, pdf_y
```

## Best Practices

1. **Always validate visually** before filling production forms
2. **Use consistent DPI** (200 recommended) throughout the workflow
3. **Test with sample data** before processing batch forms
4. **Keep original PDFs** - annotations are non-destructive
5. **Handle multi-line text** by setting appropriate height

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Text appears outside box | Wrong coordinates | Recalculate from image at same DPI |
| Text too small | Font size mismatch | Adjust font_size in fields.json |
| Checkbox not checked | Wrong value type | Use boolean `true`/`false` |
| Dropdown empty | Value doesn't match option | Use exact option text |
