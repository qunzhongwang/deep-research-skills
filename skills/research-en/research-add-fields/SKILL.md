---
user-invocable: true
description: Add field definitions to an existing research outline.
allowed-tools: Bash, Read, Write, Glob, WebSearch, Task, AskUserQuestion
---

# Research Add Fields — Expand Research Fields

## Trigger
`/research-add-fields`

## Workflow

### Step 1: Locate Fields File
Find `*/fields.yaml` in the current working directory and read the existing field definitions.

### Step 2: Gather New Fields
Ask user to choose:
- **A. Direct input**: User provides field names and descriptions
- **B. Web search**: Launch an agent to find commonly tracked fields in this domain

### Step 3: Review and Confirm
- Show suggested new fields
- User confirms which fields to add
- User specifies field category and detail_level for each

### Step 4: Save
Append confirmed fields to fields.yaml.

## Output
Updated `{topic}/fields.yaml` (in-place, requires user confirmation)
