# ZIRU-SKILLS

Custom skills (slash commands) for [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code).

Skills are Markdown-based prompt templates that extend Claude Code with reusable workflows. Place them in `~/.claude/commands/` to make them available as `/skill-name` in any project.

## Available Skills

| Skill | Description | Prerequisites |
|-------|-------------|---------------|
| [repomix_nlm_init](commands/repomix_nlm_init.md) | Analyze codebase with Repomix and create/update a NotebookLM knowledge base | Repomix MCP, NotebookLM MCP |

## Installation

### Quick Install (Single Skill)

Download a skill file directly into your Claude Code commands directory:

```bash
# Create commands directory if it doesn't exist
mkdir -p ~/.claude/commands

# Download a specific skill
curl -o ~/.claude/commands/repomix_nlm_init.md \
  https://raw.githubusercontent.com/zirubak/ZIRU-SKILLS/main/commands/repomix_nlm_init.md
```

### Install All Skills

```bash
# Clone the repo
git clone https://github.com/zirubak/ZIRU-SKILLS.git

# Copy all skills to Claude Code commands directory
cp ZIRU-SKILLS/commands/*.md ~/.claude/commands/
```

### Verify

After installation, type `/repomix_nlm_init` in Claude Code to confirm the skill is available.

## Skill Format

Each skill follows the [Claude Code custom slash commands](https://docs.anthropic.com/en/docs/claude-code/tutorials/custom-slash-commands) format:

```markdown
---
description: Short description shown in the skill list
argument-hint: "Expected argument (e.g., project name)"
---

# Skill Title

Instructions for Claude Code to follow when this skill is invoked.

$ARGUMENTS
```

- `$ARGUMENTS` is replaced with whatever the user types after the slash command
- Skills can reference MCP tools, other skills, and any Claude Code capability

## Repository Structure

```
ZIRU-SKILLS/
  commands/           # Skill files (copy to ~/.claude/commands/)
    repomix_nlm_init.md
  README.md
```

## License

Feel free to use, modify, and share these skills.
