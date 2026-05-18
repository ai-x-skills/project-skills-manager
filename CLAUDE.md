# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A Claude Code skill for intelligent per-project plugin management. Instead of all projects loading all installed skills, `project-skills-manager` analyzes each project's tech stack and recommends which skills to enable/disable using the official `claude plugin` CLI.

## Key Files

- `.claude-plugin/marketplace.json` — Marketplace manifest. Makes this repo a valid Claude Code marketplace.
- `.claude-plugin/plugin.json` — Plugin manifest for official system recognition.
- `SKILL.md` — The skill instructions. Defines the workflow: diagnose project tech stack → recommend skills → execute via `claude plugin` CLI → verify.
- `references/skill-catalog.md` — Reference data for skill categorization.
- `README.md` — Full documentation in Chinese.

## How It Works

The skill uses Claude Code's official plugin system:

- `claude plugin install {name} --scope project` — Install skill for current project only
- `claude plugin disable {name} --scope project` — Disable global skill in current project (stored in `{project}/.claude/settings.json`)
- `claude plugin enable {name} --scope project` — Re-enable disabled skill

Project-level disable does not affect other projects.

## Architecture

```
project-skills-manager (intelligence)
    ↓ analyzes
Project tech stack (Glob + Read)
    ↓ matches against
references/skill-catalog.md (recommendation data)
    ↓ executes via
claude plugin CLI (official plugin system)
```

## Dependencies

- **Claude Code CLI** — `claude plugin` commands

## Modifying the Skill

When editing `SKILL.md`:

- The description field is the trigger mechanism — include both positive triggers and exclusions
- Steps describe intent, not scripts — Claude assembles commands from the instructions
- Safety rules in the SKILL.md must be preserved: project-scope operations are safe, global-scope requires user confirmation
- The `references/skill-catalog.md` should be updated when new skills become available
