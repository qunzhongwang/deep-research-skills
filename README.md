# Deep Research Skill for Claude Code

A structured research workflow for Claude Code with two phases: outline generation and deep investigation. Human-in-the-loop design gives you control at every stage.

> Inspired by [RhinoInsight: Improving Deep Research through Control Mechanisms for Model Behavior and Context](https://arxiv.org/abs/2511.18743)

## Use Cases

- **Academic Research** — Paper surveys, benchmark reviews, literature analysis
- **Technical Research** — Technology comparison, framework evaluation, tool selection
- **Market Research** — Competitor analysis, industry trends, product comparison
- **Due Diligence** — Company research, investment analysis, risk assessment

## Installation

```bash
git clone https://github.com/Weizhena/deep-research-skills.git
cd deep-research-skills

# Install skills
cp -r skills/research-en/* ~/.claude/skills/

# Install agent and search modules
cp agents/web-search-agent.md ~/.claude/agents/
cp -r agents/web-search-modules ~/.claude/agents/

# Install Python dependency
pip install pyyaml
```

## Commands

> Claude Code 2.1.0+ supports direct `/skill-name` triggers. Older versions: use `run /skill-name`.

| Command | Description |
|---|---|
| `/research` | Generate a research outline with items and fields |
| `/research-add-items` | Add more items to an existing outline |
| `/research-add-fields` | Add more field definitions to an existing outline |
| `/research-deep` | Deep-research each item with parallel agents |
| `/research-report` | Generate a markdown report from the JSON results |

## Workflow

> Example: Researching "AI Agent Demo 2025"

### Phase 1 — Generate Outline

```
/research AI Agent Demo 2025
```

Give it your topic and it builds a research outline: a list of items to investigate and the fields to collect for each.

**Output**: `outline.yaml` (items + config) and `fields.yaml` (field definitions)

### Phase 1.5 — Expand (optional)

```
/research-add-items
/research-add-fields
```

Add more items or fields to the outline before starting deep research.

### Phase 2 — Deep Research

```
/research-deep
```

Launches parallel web search agents to research each item, writing structured JSON for every entry.

**Output**: One JSON file per item in the results directory

### Phase 3 — Generate Report

```
/research-report
```

Compiles all JSON results into a single markdown report with table of contents.

**Output**: `report.md` — ready to read or share

## References

- [RhinoInsight: Improving Deep Research through Control Mechanisms for Model Behavior and Context](https://arxiv.org/abs/2511.18743)
- Modified from [DeepResearchLab/deep-research-skills](https://github.com/DeepResearchLab/deep-research-skills)

## License

MIT
