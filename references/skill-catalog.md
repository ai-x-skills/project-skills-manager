# Skill Catalog

> Static skill-to-category mapping for fast recommendation. For skills not listed here, fall back to reading their SKILL.md description.

| Category | Skill | Source |
|----------|-------|--------|
| Frontend | `frontend-design` | `[Anthropic]` |
| Frontend | `impeccable` | `[npx]` |
| Frontend | `design-taste-frontend` | `[npx]` |
| Frontend | `web-artifacts-builder` | `[Anthropic]` |
| Frontend | `imagegen-frontend-web` | `[npx]` |
| Backend | `fastapi-python` | `[npx]` |
| Backend | `postgresql-database-engineering` | `[npx]` |
| Backend | `claude-api` | `[Anthropic]` |
| Scraping | `scrapling` | `[custom]` |
| Scraping | `scrapy-web-scraping` | `[npx]` |
| Scraping | `playwright-scraper` | `[npx]` |
| Testing | `playwright-e2e-testing` | `[npx]` |
| Testing | `webapp-testing` | `[Anthropic]` |
| DevOps | `observability-monitoring` | `[npx]` |
| Document | `pdf` | `[Anthropic]` |
| Document | `docx` | `[Anthropic]` |
| Document | `pptx` | `[Anthropic]` |
| Document | `xlsx` | `[Anthropic]` |
| Document | `doc-coauthoring` | `[Anthropic]` |
| Document | `guizang-ppt-skill` | `[custom]` |
| Design | `canvas-design` | `[Anthropic]` |
| Design | `brand-guidelines` | `[Anthropic]` |
| Design | `theme-factory` | `[Anthropic]` |
| Design | `algorithmic-art` | `[Anthropic]` |
| Design | `slack-gif-creator` | `[Anthropic]` |
| AI | `chroma` | `[npx]` |
| AI | `understand-anything-knowledge-graph` | `[npx]` |
| AI | `consciousness-transfer` | `[npx]` |
| DevTools | `mcp-builder` | `[Anthropic]` |
| DevTools | `internal-comms` | `[Anthropic]` |
| DevTools | `skill-creator` | `[Anthropic]` |

## Source Types

| Tag | Meaning | Installation path | Management |
|-----|---------|-------------------|------------|
| `[Anthropic]` | Anthropic official marketplace | `~/.agents/skills/` (via `claude plugin install`) | `claude plugin disable/enable --scope project` |
| `[npx]` | npx global install | `~/.agents/skills/` (via `npx skills add`) | Register as `npx-others-skills` marketplace → `claude plugin disable/enable --scope project` |
| `[custom]` | Custom marketplace | `~/.agents/skills/` (via `claude plugin install` from custom marketplace) | `claude plugin disable/enable --scope project` |
| `[offline]` | Offline/manual install | `~/.claude/local-skills/` | Register as marketplace source → `claude plugin disable/enable --scope project` |
