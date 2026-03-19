---
user-invocable: true
allowed-tools: Read, Write, Glob, WebSearch, Task, AskUserQuestion
description: Conduct preliminary research on a topic and generate a research outline. For academic surveys, benchmarks, technology selection, and more.
---

# Research Skill — Preliminary Research

## Trigger
`/research <topic>`

## Workflow

### Step 1: Generate Initial Framework from Model Knowledge
Based on the topic, use the model's existing knowledge to generate:
- A list of key items (entities, products, papers, etc.) in this domain
- A suggested set of research fields to investigate for each item

Present the output as {step1_output} and use AskUserQuestion to confirm:
- Any items to add or remove?
- Does the field framework meet your needs?

### Step 2: Web Search to Expand Coverage
Use AskUserQuestion to ask for a time range (e.g., last 6 months, since 2024, no limit).

**Variables**:
- `{topic}`: User's research topic
- `{YYYY-MM-DD}`: Current date
- `{step1_output}`: Complete output from Step 1
- `{time_range}`: User-specified time range

**Important**: Use the following prompt template exactly, only replacing `{variables}`.

Launch 1 web-search-agent (background). **Prompt template**:
```python
prompt = f"""## Task
Research topic: {topic}
Current date: {YYYY-MM-DD}

Expand and validate the following initial framework with up-to-date information.

## Existing Framework
{step1_output}

## Goals
1. Check whether the existing items are missing anything important
2. Add any missing items
3. Search for additional {topic} items within {time_range} and add them
4. Suggest additional research fields worth tracking

## Output Requirements
Return structured results directly (do not write files):

### Additional Items
- item_name: Brief explanation of why it should be included
...

### Suggested Additional Fields
- field_name: What this field captures and why it matters
...

### Sources
- [Source1](url1)
- [Source2](url2)
"""
```

**Example** (topic: AI Coding History):
```
## Task
Research topic: AI Coding History
Current date: 2025-12-30

Expand and validate the following initial framework with up-to-date information.

## Existing Framework
### Items
1. GitHub Copilot: Developed by Microsoft/GitHub, first mainstream AI coding assistant
2. Cursor: AI-first IDE, based on VSCode
...

### Fields
- Basic Info: name, release_date, company
- Technical Features: underlying_model, context_window
...

## Goals
1. Check whether the existing items are missing anything important
2. Add any missing items
3. Search for additional AI Coding History items within since 2024 and add them
4. Suggest additional research fields worth tracking

## Output Requirements
Return structured results directly (do not write files):

### Additional Items
- item_name: Brief explanation of why it should be included
...

### Suggested Additional Fields
- field_name: What this field captures and why it matters
...

### Sources
- [Source1](url1)
- [Source2](url2)
```

### Step 3: Ask About Existing Field Definitions
Use AskUserQuestion to ask if the user has an existing field definition file. If so, read and merge it.

### Step 4: Generate Outline (Two Separate Files)
Merge {step1_output}, {step2_output}, and any existing user fields into two files:

**outline.yaml** (items + config):
- topic: Research topic
- items: List of research items
- execution:
  - batch_size: Number of parallel agents (confirm with AskUserQuestion)
  - items_per_agent: Items per agent (confirm with AskUserQuestion)
  - output_dir: Results output directory (default: ./results)

**fields.yaml** (field definitions):
- Field categories and definitions
- Each field: name, description, detail_level
- detail_level hierarchy: brief -> moderate -> detailed
- uncertain: Reserved list for fields marked uncertain during deep research

### Step 5: Save and Confirm
- Create directory: `./{topic_slug}/`
- Save: `outline.yaml` and `fields.yaml`
- Show both files to user for confirmation

## Output Path
```
{current_working_directory}/{topic_slug}/
  ├── outline.yaml    # Items list + execution config
  └── fields.yaml     # Field definitions
```

## Follow-up Commands
- `/research-add-items` — Add more items
- `/research-add-fields` — Add more fields
- `/research-deep` — Start deep research
