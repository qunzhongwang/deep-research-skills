---
user-invocable: true
description: Read the research outline and launch independent agents for deep research on each item. Runs in background with task output disabled.
allowed-tools: Bash, Read, Write, Glob, WebSearch, Task
---

# Research Deep — Deep Research

## Trigger
`/research-deep`

## Workflow

### Step 1: Locate Outline
Find `*/outline.yaml` in the current working directory. Read the items list and execution config (including items_per_agent).

### Step 2: Resume Check
- Check for completed JSON files in output_dir
- Skip already-completed items

### Step 3: Batch Execution
- Process items in batches of batch_size (ask user before starting each batch)
- Each agent handles items_per_agent items
- Launch web-search-agent instances in background (task output disabled)

**Variables**:
- `{topic}`: topic field from outline.yaml
- `{item_name}`: Item's name field
- `{item_related_info}`: Item's complete YAML content (name + category + description, etc.)
- `{output_dir}`: execution.output_dir from outline.yaml (default: ./results)
- `{fields_path}`: Absolute path to {topic}/fields.yaml
- `{output_path}`: Absolute path to {output_dir}/{item_name_slug}.json (slugify: replace spaces with `_`, strip special chars)

**Important**: Use the following prompt template exactly, only replacing `{variables}`.

**Prompt template**:
```python
prompt = f"""## Task
Research {item_related_info}, output structured JSON to {output_path}

## Field Definitions
Read {fields_path} to get all field definitions.

## Output Requirements
1. Output JSON with all fields defined in fields.yaml
2. Mark uncertain values with [uncertain]
3. Add an "uncertain" array at the end of the JSON listing all uncertain field names
4. All field values must be in English

## Output Path
{output_path}

## Validation
After writing the JSON, run the validation script to ensure complete field coverage:
python ~/.claude/skills/research/validate_json.py -f {fields_path} -j {output_path}
The task is complete only after validation passes.
"""
```

**Example** (item: GitHub Copilot):
```
## Task
Research name: GitHub Copilot
category: International Product
description: Developed by Microsoft/GitHub, first mainstream AI coding assistant, ~40% market share, output structured JSON to /home/user/aicoding-history/results/GitHub_Copilot.json

## Field Definitions
Read /home/user/aicoding-history/fields.yaml to get all field definitions.

## Output Requirements
1. Output JSON with all fields defined in fields.yaml
2. Mark uncertain values with [uncertain]
3. Add an "uncertain" array at the end of the JSON listing all uncertain field names
4. All field values must be in English

## Output Path
/home/user/aicoding-history/results/GitHub_Copilot.json

## Validation
After writing the JSON, run the validation script to ensure complete field coverage:
python ~/.claude/skills/research/validate_json.py -f /home/user/aicoding-history/fields.yaml -j /home/user/aicoding-history/results/GitHub_Copilot.json
The task is complete only after validation passes.
```

### Step 4: Wait and Monitor
- Wait for current batch to complete
- Launch next batch
- Display progress

### Step 5: Summary
After all items complete, report:
- Total completed
- Items with failures or uncertain fields
- Output directory path

## Agent Config
- Background execution: Yes
- Task output: Disabled (each agent writes its own output file)
- Resume support: Yes
