# Late-Script-
Pulling all lates, including Name, Date, Hours, Notes 
## Overview
This script creates a new weekly attendance sheet by extracting LAT-Late entries from a PDF attendance report, formatting the data as an Excel table, and validating against prior sheets for notes and highlighting carryforward.

---

## Prerequisites
Each week, upload:
1. **PDF Attendance Report** (e.g., `8-3-26 Full Attendance Report.PDF`)
2. **Excel Workbook** with prior weekly sheets (e.g., `8-3-2026.xlsx`)

---

## Step 1: Parse the PDF Report

### 1.1 Extract PDF Header Date Window
From the PDF header, find: `FOR PERIOD <date1> THRU <date2>`

Calculate the date window:

**Guardrail:** Ignore any AbsenceDate more than 45 days older than ReportRunDate or later than WindowEnd.

### 1.2 Extract LAT-Late Entries
Parse the PDF using token-based scanning (not line-based) to handle:
- Employee blocks spanning page breaks
- Wrapped/continuation lines
- Comments after dates

**Inclusion Rules:**
Keep only occurrences where AbsenceDate is within WindowStart..WindowEnd AND either:
1. NormalizedReason = "LAT-Late"
2. OR NormalizedReason starts with "LAT-" AND CommentText explicitly indicates lateness (contains "late", "late arrival", "arrived late", "was late", "LAT-Late")

**Normalization:**
- Trim whitespace, collapse multiple spaces
- Case-insensitive comparison
- Normalize separators (hyphens, spaces)

### 1.3 Build Expected Set
For each included occurrence, create tuple:

**Sanity Checks:**
- Confirm no dates outside WindowStart..WindowEnd
- Confirm every entry has LAT-Late reason or lateness-indicating comment
- Spot-check 3+ employee names from different pages

---

## Step 2: Create New Worksheet

### 2.1 Name the Sheet
Use the ReportRunDate in format: `M-D-YY` (e.g., `8-3-26` for August 3, 2026)

### 2.2 Set Up Columns
Create exactly 4 columns in this order:
| Column | Header | Content |
|--------|--------|---------|
| A | Name | Employee name as printed in PDF |
| B | Date | Absence date (MM/DD/YYYY format, as text) |
| C | Hours | Hours value (numeric) |
| D | Notes | Carried forward from prior sheets (see Step 3) |

### 2.3 Populate Data
Insert one row per LAT-Late entry from the Expected Set.
- Do NOT add formulas
- Do NOT add totals rows
- Preserve exact values from PDF

---

## Step 3: Notes Carryforward

For each row in the new sheet:
1. Search the **three worksheets immediately before** the current sheet
2. Look for a matching prior row using: **same Name AND same Date**
3. Search starting with the closest previous tab, stop at first populated Notes value
4. If a prior note exists, carry it forward to the Notes column
5. If no prior note exists, leave Notes blank

---

## Step 4: Highlighting Carryforward

For each row in the new sheet:
1. Check **ONLY the single worksheet immediately before** the current sheet
2. Look for a matching prior row using: **same Name AND same Date AND same Hours**
3. If that matching prior row is highlighted, apply the same highlight color to the current row
4. If no exact match or not highlighted, leave unhighlighted

---

## Step 5: Format the Worksheet

### 5.1 Convert to Excel Table
- Table name: `Table_<sheet_name>` with underscores (e.g., `Table_8_3_26`)
- Position: Starting at A1

### 5.2 Apply Table Style
- NO banded rows
- NO banded columns

### 5.3 Sort the Table
- Sort by Date column: **HIGHEST to LOWEST** (newest dates first)

### 5.4 Apply Formatting
| Format | Target |
|--------|--------|
| Borders | ALL table cells (including header) |
| Bold | Header row only |
| Center alignment | Columns B and C |
| Auto-size | All columns to fit text |

### 5.5 Preserve Highlighting
- Do NOT remove any carried-forward highlighting
- Do NOT add new highlighting

---

## Step 6: Validation

### 6.1 Column Validation
Confirm exactly 4 columns: Name, Date, Hours, Notes (in that order)

### 6.2 Value Integrity
- All Date values are valid calendar dates
- All Hours values are numeric (materialized values only)
- No cells contain formulas
- No totals rows or summary rows
- Notes column exists for every row (may be blank)

### 6.3 Compare to Expected Set

### 6.4 Repair Action (if needed)
If Missing is non-empty:
- APPEND missing rows at bottom (do NOT insert or sort after)
- Apply notes carryforward to appended rows
- Apply highlighting carryforward to appended rows only if exact match exists

### 6.5 Duplicate Detection
Flag any rows where (Name, Date, Hours) are identical (do not delete)

### 6.6 Output
