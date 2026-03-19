---
user-invocable: true
description: Generate a markdown report from deep research results, covering all fields and skipping uncertain values.
allowed-tools: Read, Write, Glob, Bash, AskUserQuestion
---

# Research Report — Generate Summary Report

## Trigger
`/research-report`

## Workflow

### Step 1: Locate Results Directory
Find `*/outline.yaml` in the current working directory. Read the topic and output_dir config.

### Step 2: Select Summary Fields for Table of Contents
Read all JSON results and identify fields suitable for TOC display (numeric values, short metrics), e.g.:
- github_stars
- google_scholar_cites
- swe_bench_score
- user_scale
- valuation
- release_date

Use AskUserQuestion to ask:
- Which fields to display in the TOC alongside item names?
- Provide a dynamic options list based on the actual fields in the JSON files

### Step 3: Generate Python Conversion Script
Generate `generate_report.py` in the `{topic}/` directory. Script requirements:
- Read all JSON files from output_dir
- Read fields.yaml to get the field structure
- Include all field values from each JSON
- Skip fields with values containing `[uncertain]`
- Skip fields listed in the `uncertain` array
- Generate markdown report: Table of contents (with anchor links + user-selected summary fields) + Detailed content (organized by field category)
- Save to `{topic}/report.md`

**TOC format**:
- Must include every item
- Each entry shows: number, name (as anchor link), user-selected summary fields
- Example: `1. [GitHub Copilot](#github-copilot) - Stars: 10k | Score: 85%`

#### Script Technical Requirements

**1. JSON Structure Compatibility**
Support two JSON layouts:
- Flat: Fields at top level `{"name": "xxx", "release_date": "xxx"}`
- Nested: Fields inside category objects `{"basic_info": {"name": "xxx"}, "technical_features": {...}}`

Field lookup order: top level -> category key mapping -> traverse all nested objects

**2. Category Key Mapping**
Map fields.yaml category names to their possible JSON key variants:
```python
CATEGORY_MAPPING = {
    "Basic Info": ["basic_info", "Basic Info"],
    "Technical Features": ["technical_features", "technical_characteristics", "Technical Features"],
    "Performance Metrics": ["performance_metrics", "performance", "Performance Metrics"],
    "Milestone Significance": ["milestone_significance", "milestones", "Milestone Significance"],
    "Business Info": ["business_info", "commercial_info", "Business Info"],
    "Competition & Ecosystem": ["competition_ecosystem", "competition", "Competition & Ecosystem"],
    "History": ["history", "History"],
    "Market Positioning": ["market_positioning", "market", "Market Positioning"],
}
```

**3. Complex Value Formatting**
- List of dicts (e.g., key_events, funding_history): Format each dict as one line, join key-value pairs with ` | `
- Simple lists: Short lists joined with commas; long lists displayed with line breaks
- Nested dicts: Recursive formatting with semicolons or line breaks
- Long strings (>100 chars): Use `<br>` line breaks or blockquote format for readability

**4. Extra Fields Collection**
Collect fields present in JSON but not defined in fields.yaml — place them in an "Other Info" category. Filter out:
- Internal fields: `_source_file`, `uncertain`
- Nested structure top-level keys: `basic_info`, `technical_features`, etc.
- `uncertain` array: Display each field name on a separate line

**5. Uncertain Value Skipping**
Skip when:
- Field value contains the string `[uncertain]`
- Field name appears in the `uncertain` array
- Field value is None or empty string

### Step 4: Execute Script
Run `python {topic}/generate_report.py`

## Output
- `{topic}/generate_report.py` — Conversion script
- `{topic}/report.md` — Final summary report
