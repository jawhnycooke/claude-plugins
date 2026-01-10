# HTML to PowerPoint Guide

Convert HTML slides to PowerPoint presentations with accurate positioning using the `html2pptx.js` library.

## Layout Dimensions

Every HTML slide must include proper body dimensions:

- **16:9** (default): `width: 720pt; height: 405pt`
- **4:3**: `width: 720pt; height: 540pt`
- **16:10**: `width: 720pt; height: 450pt`

## Supported Elements

| Element | PowerPoint Output |
|---------|-------------------|
| `<p>`, `<h1>`-`<h6>` | Text with styling |
| `<ul>`, `<ol>` | Lists (never use manual bullets) |
| `<b>`, `<strong>` | Bold text |
| `<i>`, `<em>` | Italic text |
| `<u>` | Underlined text |
| `<span>` | Inline formatting |
| `<br>` | Line breaks |
| `<div>` with bg/border | Shapes |
| `<img>` | Images |
| `class="placeholder"` | Chart/content placeholder |

## Critical Rules

### Text Must Be in Proper Tags

**ALL text MUST be inside `<p>`, `<h1>`-`<h6>`, `<ul>`, or `<ol>` tags:**

```html
<!-- ✅ Correct -->
<div><p>Text here</p></div>

<!-- ❌ Wrong - text will NOT appear -->
<div>Text here</div>
<span>Text</span>
```

### Web-Safe Fonts Only

Only use universally available fonts:
- ✅ Arial, Helvetica, Times New Roman, Georgia, Courier New, Verdana, Tahoma, Trebuchet MS

```css
/* ❌ Wrong - may cause rendering issues */
font-family: 'Segoe UI', 'SF Pro', 'Roboto';
```

### No Manual Bullets

**NEVER use bullet symbols (•, -, *, etc.)** - Use `<ul>` or `<ol>`:

```html
<!-- ✅ Correct -->
<ul>
  <li>First item</li>
  <li>Second item</li>
</ul>

<!-- ❌ Wrong -->
<p>• First item</p>
<p>• Second item</p>
```

### No CSS Gradients

CSS gradients don't convert - rasterize to PNG first:

```javascript
const sharp = require('sharp');

async function createGradient(filename) {
    const svg = `<svg xmlns="http://www.w3.org/2000/svg" width="1000" height="562">
        <defs>
            <linearGradient id="g" x1="0%" y1="0%" x2="100%" y2="100%">
                <stop offset="0%" style="stop-color:#4472C4"/>
                <stop offset="100%" style="stop-color:#2C3E50"/>
            </linearGradient>
        </defs>
        <rect width="100%" height="100%" fill="url(#g)"/>
    </svg>`;

    await sharp(Buffer.from(svg)).png().toFile(filename);
}
```

## Shape Styling (DIV Only)

Backgrounds, borders, and shadows only work on `<div>` elements:

```html
<div style="
    background: #f0f0f0;
    border: 2px solid #333333;
    border-radius: 8pt;
    box-shadow: 2px 2px 8px rgba(0, 0, 0, 0.3);
">
    <p>Content inside shape</p>
</div>
```

**Not supported on text elements (`<p>`, `<h1>`, etc.)**

## Basic Usage

```javascript
const pptxgen = require('pptxgenjs');
const html2pptx = require('./html2pptx');

const pptx = new pptxgen();
pptx.layout = 'LAYOUT_16x9';

const { slide, placeholders } = await html2pptx('slide.html', pptx);

// Add chart to placeholder
if (placeholders.length > 0) {
    slide.addChart(pptx.charts.BAR, chartData, {
        ...placeholders[0],
        showTitle: true,
        title: 'Sales Data'
    });
}

await pptx.writeFile('output.pptx');
```

## Placeholders

Use `class="placeholder"` to reserve space for dynamic content:

```html
<div id="chart" class="placeholder" style="width: 350pt; height: 200pt;"></div>
```

Returns position for programmatic content:
```javascript
{ id: "chart", x: 1.5, y: 2.0, w: 4.86, h: 2.78 }
```

## HTML Template Example

```html
<!DOCTYPE html>
<html>
<head>
<style>
html { background: #ffffff; }
body {
    width: 720pt;
    height: 405pt;
    margin: 0;
    padding: 0;
    font-family: Arial, sans-serif;
    display: flex;
}
.content {
    margin: 30pt;
    padding: 40pt;
    background: #ffffff;
    border-radius: 8pt;
}
h1 { color: #2d3748; font-size: 32pt; }
.stat-box {
    background: #4472C4;
    padding: 20pt;
    border-radius: 8pt;
}
.stat-box p { color: #ffffff; font-size: 24pt; }
</style>
</head>
<body>
<div class="content">
    <h1>Quarterly Report</h1>
    <ul>
        <li>Revenue increased 15%</li>
        <li>Customer satisfaction at 92%</li>
    </ul>
    <div class="stat-box">
        <p>$1.2M</p>
    </div>
    <div id="chart" class="placeholder" style="width: 350pt; height: 200pt;"></div>
</div>
</body>
</html>
```

## Adding Charts with PptxGenJS

### Bar Chart

```javascript
slide.addChart(pptx.charts.BAR, [{
    name: "Sales 2024",
    labels: ["Q1", "Q2", "Q3", "Q4"],
    values: [4500, 5500, 6200, 7100]
}], {
    ...placeholders[0],
    barDir: 'col',
    showTitle: true,
    title: 'Quarterly Sales',
    showCatAxisTitle: true,
    catAxisTitle: 'Quarter',
    showValAxisTitle: true,
    valAxisTitle: 'Sales ($000s)',
    chartColors: ["4472C4"]  // No # prefix!
});
```

### Line Chart

```javascript
slide.addChart(pptx.charts.LINE, [{
    name: "Temperature",
    labels: ["Jan", "Feb", "Mar", "Apr"],
    values: [32, 35, 42, 55]
}], {
    ...placeholders[0],
    lineSize: 4,
    lineSmooth: true,
    showCatAxisTitle: true,
    catAxisTitle: 'Month',
    showValAxisTitle: true,
    valAxisTitle: 'Temperature (°F)',
    chartColors: ["4472C4", "ED7D31"]
});
```

### Pie Chart

```javascript
slide.addChart(pptx.charts.PIE, [{
    name: "Market Share",
    labels: ["Product A", "Product B", "Other"],
    values: [35, 45, 20]
}], {
    ...placeholders[0],
    showPercent: true,
    showLegend: true,
    legendPos: 'r',
    chartColors: ["4472C4", "ED7D31", "A5A5A5"]
});
```

## Critical PptxGenJS Rules

### Colors Without # Prefix

```javascript
// ✅ Correct
chartColors: ["4472C4", "ED7D31"]
color: "FF0000"

// ❌ Wrong - causes corruption
chartColors: ["#4472C4"]
color: "#FF0000"
```

### Image Dimensions

Calculate from actual image:

```javascript
const imgWidth = 1860, imgHeight = 1519;
const aspectRatio = imgWidth / imgHeight;

const h = 3;
const w = h * aspectRatio;
const x = (10 - w) / 2;  // Center on slide

slide.addImage({ path: "chart.png", x, y: 1.5, w, h });
```

## Validation

The library automatically validates:
- HTML dimensions match presentation layout
- Content doesn't overflow body
- No unsupported CSS gradients
- No backgrounds/borders on text elements

All errors reported together for easy fixing.

## Dependencies

```bash
npm install pptxgenjs playwright sharp react-icons
```

System requirements:
- LibreOffice (for PDF conversion in thumbnails)
- Poppler utilities
