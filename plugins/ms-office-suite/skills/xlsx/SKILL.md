---
name: analyzing-spreadsheets
description: Creates, edits, and analyzes Excel spreadsheets (.xlsx, .xlsm, .csv), including formula-based calculations, cell formatting, financial modeling, and data analysis with pandas and openpyxl. Activates when the user works with spreadsheet files or requests Excel-related tasks.
---

# Excel Spreadsheet (.xlsx) Guide

This guide covers creating, editing, and analyzing Excel files with a focus on formula-based calculations and professional formatting.

## Critical Requirements

### Zero Formula Errors

All deliverables must have **ZERO formula errors**:
- `#REF!` - Invalid cell references
- `#DIV/0!` - Division by zero
- `#VALUE!` - Wrong data type
- `#N/A` - Value not available
- `#NAME?` - Unrecognized formula name

### Use Formulas, Not Hardcoded Values

**Always use Excel formulas instead of calculating values in Python.**

```python
# ❌ WRONG - hardcoding calculated values
total = df['Sales'].sum()
sheet['B10'] = total  # Hardcodes 5000

# ✅ CORRECT - using Excel formulas
sheet['B10'] = '=SUM(B2:B9)'
```

This ensures spreadsheets remain dynamic and updateable.

## Library Selection

| Use Case | Library |
|----------|---------|
| Data analysis, bulk operations | pandas |
| Formulas, complex formatting | openpyxl |
| Formula recalculation | recalc.py script |

## Common Workflow

1. **Choose tool**: pandas for data, openpyxl for formulas
2. **Create/Load**: Create new or load existing workbook
3. **Modify**: Add data, formulas, and formatting
4. **Save**: Write to file
5. **Recalculate** (if using formulas): `python recalc.py output.xlsx`
6. **Verify**: Check for errors and fix

## Creating Excel Files

### With openpyxl (Formulas & Formatting)

```python
from openpyxl import Workbook
from openpyxl.styles import Font, PatternFill, Alignment, Border, Side

wb = Workbook()
sheet = wb.active
sheet.title = "Sales Data"

# Add headers
headers = ['Product', 'Q1', 'Q2', 'Q3', 'Q4', 'Total']
for col, header in enumerate(headers, 1):
    cell = sheet.cell(row=1, column=col, value=header)
    cell.font = Font(bold=True)
    cell.fill = PatternFill('solid', start_color='CCCCCC')

# Add data
data = [
    ['Product A', 1000, 1200, 1100, 1300],
    ['Product B', 800, 900, 950, 1000],
    ['Product C', 1500, 1600, 1550, 1700],
]

for row_idx, row_data in enumerate(data, 2):
    for col_idx, value in enumerate(row_data, 1):
        sheet.cell(row=row_idx, column=col_idx, value=value)
    # Add formula for row total
    sheet.cell(row=row_idx, column=6, value=f'=SUM(B{row_idx}:E{row_idx})')

# Add column totals
for col in range(2, 7):
    col_letter = chr(64 + col)
    sheet.cell(row=5, column=col, value=f'=SUM({col_letter}2:{col_letter}4)')

wb.save('sales.xlsx')
```

### With pandas (Data Analysis)

```python
import pandas as pd

# Create DataFrame
df = pd.DataFrame({
    'Product': ['A', 'B', 'C'],
    'Sales': [1000, 800, 1500],
    'Region': ['North', 'South', 'East']
})

# Export to Excel
df.to_excel('output.xlsx', index=False, sheet_name='Data')

# Multiple sheets
with pd.ExcelWriter('multi_sheet.xlsx') as writer:
    df.to_excel(writer, sheet_name='Sales', index=False)
    summary = df.groupby('Region').sum()
    summary.to_excel(writer, sheet_name='Summary')
```

## Editing Existing Files

```python
from openpyxl import load_workbook

# Load preserving formulas
wb = load_workbook('existing.xlsx')
sheet = wb.active

# Modify cells
sheet['A1'] = 'New Value'

# Add new rows
sheet.insert_rows(2)
sheet['A2'] = 'Inserted row'

# Add new sheet
new_sheet = wb.create_sheet('Analysis')
new_sheet['A1'] = '=Sheet1!A1'  # Cross-sheet reference

wb.save('modified.xlsx')
```

**Warning**: Opening with `data_only=True` replaces formulas with values permanently.

## Financial Model Standards

### Color Coding Convention

| Color | Usage |
|-------|-------|
| Blue text | Hardcoded inputs |
| Black text | Formulas |
| Green text | Cross-sheet links |
| Red text | External references |
| Yellow background | Key assumptions |

### Number Formatting

```python
from openpyxl.styles import numbers

# Currency
sheet['B2'].number_format = '$#,##0'

# Percentages
sheet['C2'].number_format = '0.0%'

# Years as text
sheet['A2'].number_format = '@'  # Text format
sheet['A2'] = '2024'

# Negative in parentheses
sheet['D2'].number_format = '$#,##0_);($#,##0)'

# Zeros as dash
sheet['E2'].number_format = '$#,##0;-$#,##0;"-"'
```

### Assumptions Section

All growth rates and multiples should:
- Be in separate cells (not embedded in formulas)
- Have proper documentation
- Include source, date, and URL where applicable

```python
# Good: Reference assumption cells
sheet['B10'] = '=B9*(1+$G$2)'  # G2 contains growth rate

# Bad: Hardcoded in formula
sheet['B10'] = '=B9*1.05'  # Where does 5% come from?
```

## Formula Recalculation

Excel files created by openpyxl contain formulas as strings but not calculated values. Use the recalc script:

```bash
python recalc.py output.xlsx [timeout_seconds]
```

### Script Output

```json
{
  "status": "success",
  "total_errors": 0,
  "total_formulas": 42
}
```

Or with errors:

```json
{
  "status": "errors_found",
  "total_errors": 3,
  "error_summary": {
    "#REF!": {
      "count": 2,
      "locations": ["Sheet1!B5", "Sheet1!C10"]
    },
    "#DIV/0!": {
      "count": 1,
      "locations": ["Sheet1!D15"]
    }
  }
}
```

## Formula Verification Checklist

### Essential Verification
- [ ] Test 2-3 sample references before building full model
- [ ] Verify column mapping (column 64 = BL, not BK)
- [ ] Check row offset (DataFrame row 5 = Excel row 6)

### Common Pitfalls
- [ ] Handle NaN values with `pd.notna()`
- [ ] Check far-right columns (FY data often in columns 50+)
- [ ] Search all occurrences, not just first
- [ ] Check denominators before division formulas

### Testing Strategy
- [ ] Test formulas on 2-3 cells before applying broadly
- [ ] Verify all referenced cells exist
- [ ] Test edge cases (zero, negative, very large values)

## Best Practices

### Library Selection
- **pandas**: Data analysis, bulk operations, simple exports
- **openpyxl**: Formulas, formatting, Excel-specific features

### openpyxl Notes
- Cell indices are 1-based (A1 = row=1, column=1)
- Use `data_only=True` to read calculated values (but formulas are lost)
- For large files: `read_only=True` or `write_only=True`

### pandas Notes
- Specify dtypes: `pd.read_excel('file.xlsx', dtype={'id': str})`
- Read specific columns: `usecols=['A', 'C', 'E']`
- Handle dates: `parse_dates=['date_column']`

## Code Style

**Python code**: Minimal, concise, no unnecessary comments

**Excel files**:
- Add cell comments for complex formulas
- Document data sources for hardcoded values
- Include notes for key calculations

## Dependencies

```bash
pip install openpyxl pandas xlrd xlsxwriter

# For recalc.py
# Requires LibreOffice installed
brew install libreoffice  # macOS
apt-get install libreoffice  # Ubuntu
```
