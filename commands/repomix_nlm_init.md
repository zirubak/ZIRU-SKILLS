---
description: Analyze current project code with Repomix and create/update a NotebookLM knowledge base
argument-hint: "Project name (e.g., my-project)"
---

# Repomix -> NotebookLM Init

Pack and analyze the current project's codebase with Repomix, then search for an existing NotebookLM notebook to update or create a new one with structured sources.

---

## Prerequisites

| MCP Server | Installation |
|------------|-------------|
| Repomix | `claude mcp add --scope user repomix -- npx -y repomix --mcp` |
| NotebookLM | `uv tool install notebooklm-mcp-cli && nlm login && nlm setup add claude-code` |

## Workflow

### Step 1: Repomix Codebase Packing

Pack the current project directory with Repomix.

```
Call mcp__repomix__pack_codebase:
- directory: Current project root (auto-detected)
- compress: true (Tree-sitter based ~70% token savings)
- includePatterns: All project source code
- ignorePatterns: ["__pycache__", "*.pyc", "build/", "dist/", "node_modules/", ".git/", ".venv/", "*.lock", "*.log"]
```

Extract from packing results:
- Total file count, line count, token count
- Directory tree structure
- Key class/function signatures

### Step 2: Repomix Analysis

Use `mcp__repomix__grep_repomix_output` to extract key patterns:
- Class definitions (`class \w+`)
- Key function/method signatures
- Config files (config, settings, env)
- Test files (test_*, *_test)
- Known issues (TODO, FIXME, HACK)

### Step 3: NotebookLM Notebook Search/Create

Search for an existing notebook first. Update if found, create if not.

```
1. Call mcp__notebooklm-mcp__notebook_list to search existing notebooks
2. If a notebook matching "$ARGUMENTS" is found:
   - Call mcp__notebooklm-mcp__notebook_describe to check current sources
   - Delete outdated Repomix-related sources, replace with fresh analysis
   - (Preserve manually added sources that are not from Repomix)
3. If not found:
   - Call mcp__notebooklm-mcp__notebook_create to create a new notebook
   - Name: "$ARGUMENTS - Codebase KB"
```

### Step 4: Upload Structured Sources

Structure the Repomix analysis into categories and upload via `source_add(type="text")`:

| # | Source Name | Contents |
|---|------------|----------|
| 1 | Architecture Overview | Overall architecture, directory structure, tech stack, dependencies |
| 2 | Core Components | Key classes/modules, roles, dependency relationships |
| 3 | Data Flow & Integration | Data flow, API endpoints, external service connections |
| 4 | Testing & Quality | Test structure, coverage, code quality issues |
| 5 | Configuration & Deployment | Config files, environment variables, build/deploy settings |
| 6 | Known Issues & Status | TODO/FIXME/HACK, known bugs, development status |

Each source is written in Markdown format with file paths and line numbers.
Skip any category that has no relevant content from the analysis.

### Step 5: Upload README/CLAUDE.md

If the following files exist at the project root, upload them as separate text sources:
- `README.md` - Project description
- `CLAUDE.md` - Project development guide

### Step 6: Verification

```
Call mcp__notebooklm-mcp__notebook_query with test questions:
- "Explain the main architecture of this project"
- "What are the core modules and their roles?"

Confirm coherent responses, then report completion.
```

---

## Output

Report the following upon completion:

```
## Repomix -> NotebookLM Result

- **Project**: $ARGUMENTS
- **Repomix**: N files, N lines, N tokens (compressed)
- **NotebookLM**: [notebook name] (ID: xxx)
- **Sources**: N added/updated
- **URL**: https://notebooklm.google.com/notebook/[ID]
- **Verification**: Test query responses confirmed
```

---

## Notes

- GitHub URLs may fail with NotebookLM source_add -> use text type as workaround
- compress=true omits function bodies but is sufficient for architectural understanding
- For large projects (500+ files), use includePatterns to target key directories
- Sources are split by category due to NotebookLM per-source text length limits
- Re-running replaces only Repomix sources; manually added sources are preserved
- NotebookLM Free plan: 100 notebooks, 50 sources/notebook. Pro/Plus plans have higher or no limits.

$ARGUMENTS
