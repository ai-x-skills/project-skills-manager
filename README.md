# Project Skills Manager

[中文文档](./README.zh-CN.md)

> Smart per-project skill management for Claude Code. Analyzes project tech stack, recommends relevant skills, one-click configuration.

## What Makes This Different

| | Manual Management | Built-in Claude Prompts | **project-skills-manager** |
|---|:-:|:-:|:-:|
| Auto-scan project tech stack | ❌ | ❌ | ✅ |
| Smart matching via description | ❌ | ❌ | ✅ |
| Project-level isolation | ❌ | ❌ | ✅ |
| One-click claude plugin commands | ❌ | ❌ | ✅ |
| Safety rules (global ops need confirmation) | ❌ | ❌ | ✅ |
| Maintained skill-catalog | ❌ | ❌ | ✅ |

---

## Problem

Claude Code installs skills globally — all projects share them. No per-project control:
- A scraping project loads frontend skills; a frontend project loads loading scraping skills
- Context bloat from irrelevant skills
- Manual `claude plugin` commands are tedious

## Solution

`project-skills-manager` is a Claude Code skill that manages this automatically:

```
You say: "Manage this project's skills"
    ↓
Skill auto-scans project tech stack
    ↓
Recommends: which to add, which to disable
    ↓
You confirm
    ↓
Executes claude plugin commands
```

### Core Capabilities

| Capability | Description |
|------|------|
| Tech stack diagnosis | Scans requirements.txt, package.json, etc. to infer project type |
| Smart recommendation | Reads plugin descriptions, matches keywords against project features |
| Project-level isolation | Uses `--scope project`, no cross-project impact |
| Global skill management | Disables unnecessary global skills per project |
| Safe operations | All project-scope operations are reversible |

## Installation

### Option 1: npx (Recommended)

```bash
npx skills add ai-x-skills/project-skills-manager -g -y
```

### Option 2: GitHub marketplace

```bash
# Add marketplace
claude plugin marketplace add https://github.com/ai-x-skills/project-skills-manager.git --scope user

# Install skill
claude plugin install project-skills-manager@project-skills-manager --scope user
```

### Option 3: Local directory

```bash
# Clone repo
git clone https://github.com/ai-x-skills/project-skills-manager.git

# Add as local marketplace
claude plugin marketplace add /path/to/project-skills-manager --scope user

# Install skill
claude plugin install project-skills-manager@project-skills-manager --scope user
```

### Option 4: Manual

Copy `project-skills-manager/` directory to `~/.claude/skills/`.

## Usage

Open Claude Code in any project directory, then say:

```
Manage this project's skills
```

or:

```
What skills does this project need?
```

### Quick Commands

| Say | Effect |
|------|------|
| "Manage skills" | Full flow: diagnose → recommend → confirm → execute |
| "Add xxx skill" | Install to current project directly |
| "Disable xxx skill" | Disable global skill in current project |
| "Show current config" | View installed and disabled plugins |
| "Which skills are unused" | Diagnose and recommend disabling |

### Complete Example

> User: "Manage this project's skills"

**Step 1: Diagnose**

```
Project Diagnosis
──────────────────────
Type: Web Backend API
Language: Python
Framework: FastAPI
Tools: PostgreSQL, Docker, Redis
Tags: backend, API, database

Current Plugin Status:
  Global enabled: 12
  Project disabled: 0
  Relevant to this project: ~5
```

**Step 2: Recommend**

```
Recommended Configuration
──────────────────────

Add (project scope):
  fastapi-python@npx              ← Backend framework match
  postgresql-database-engineering@npx  ← Database tool match

Disable (project scope):
  frontend-design@anthropic-agent-skills  ← No frontend code
  algorithmic-art@anthropic-agent-skills  ← No creative coding needs

Keep:
  claude-api@anthropic-agent-skills  ← Not integrated, but potentially useful
```

**Step 3: Confirm + Execute**

> User confirms → Claude executes:

```bash
claude plugin install fastapi-python@npx --scope project
claude plugin install postgresql-database-engineering@npx --scope project
claude plugin disable frontend-design@anthropic-agent-skills --scope project
claude plugin disable algorithmic-art@anthropic-agent-skills --scope project
```

**Step 4: Verify**

```bash
claude plugin list
# Shows: fastapi-python ✅ (project), postgresql-database-engineering ✅ (project)
# Shows: frontend-design ⛔ (disabled), algorithmic-art ⛔ (disabled)
```

Other projects are completely unaffected.

---

## How It Works

### Official Plugin System

Claude Code has a complete plugin management system with three scopes:

| Scope | Command | Effect |
|--------|------|------|
| `user` (default) | `claude plugin install X` | Available globally |
| `project` | `claude plugin install X --scope project` | Current project only |
| `local` | `claude plugin install X --scope local` | Current session only |

Project-level disables are stored in `{project}/.claude/settings.json` and do not affect other projects.

### Recommendation Logic

1. Scan project files to extract language, framework, tools, and scenario features
2. Get available plugins from marketplace
3. Read each plugin's SKILL.md description and match keywords against project features
4. Classify recommendations by match strength:
   - **Strong match**: description explicitly mentions project's framework/tools → add
   - **Weak match**: related scenario, no specific framework → keep
   - **No match**: no overlap → disable
   - **Reverse match**: project clearly lacks certain code types → disable

## File Structure

```
project-skills-manager/
  .claude-plugin/
    marketplace.json           ← Marketplace manifest
    plugin.json                ← Plugin manifest
  references/
    skill-catalog.md           ← Reference data
  SKILL.md                     ← Skill instructions
  LICENSE                      ← MIT License
  README.md
  README.zh-CN.md
```

## Prerequisites

- Claude Code CLI (`claude plugin` commands available)
- **Windows users**: Ensure you're using a compatible shell (Git Bash, WSL, or similar Unix-like environment)

## Related Skills

| Skill | Purpose | Relationship |
|-------|------|------------------|
| `find-skills` | Search and install new skills | Complementary |
| `skill-creator` | Create new skills | No direct relationship |
| `update-config` | Manage Claude Code config | No direct relationship |

## License

MIT — see [LICENSE](./LICENSE)
