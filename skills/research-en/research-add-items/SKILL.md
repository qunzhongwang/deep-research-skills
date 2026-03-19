---
user-invocable: true
description: Add items to an existing research outline.
allowed-tools: Bash, Read, Write, Glob, WebSearch, Task, AskUserQuestion
---

# Research Add Items — Expand Research Items

## Trigger
`/research-add-items`

## Workflow

### Step 1: Locate Outline
Find `*/outline.yaml` in the current working directory and read it.

### Step 2: Gather New Items (in parallel)
Simultaneously:
- **A. Ask user**: What items to add? Any specific names?
- **B. Ask about web search**: Should an agent search for more items?

### Step 3: Merge and Update
- Append new items to outline.yaml
- Deduplicate against existing items
- Show updated list to user for confirmation
- Save the updated outline

## Output
Updated `{topic}/outline.yaml` (in-place)
