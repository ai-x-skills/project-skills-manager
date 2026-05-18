# Project Skills Manager

> Smart per-project skill management for Claude Code. Analyzes project tech stack, recommends relevant skills, one-click configuration.
>
> 智能管理 Claude Code 项目级 Skill 配置。分析项目技术栈，推荐相关 skill，一键配置。

## What Makes This Different / 有什么不同

| | 手动管理 | Claude 内置提示 | **project-skills-manager** |
|---|:-:|:-:|:-:|
| 自动扫描项目技术栈 | ❌ | ❌ | ✅ |
| 基于 description 智能匹配推荐 | ❌ | ❌ | ✅ |
| 项目级隔离，互不影响 | ❌ | ❌ | ✅ |
| 一键执行 claude plugin 命令 | ❌ | ❌ | ✅ |
| 安全规则（全局操作需确认） | ❌ | ❌ | ✅ |
| 维护 skill-catalog 持续更新 | ❌ | ❌ | ✅ |

| | Manual Management | Built-in Claude Prompts | **project-skills-manager** |
|---|:-:|:-:|:-:|
| Auto-scan project tech stack | ❌ | ❌ | ✅ |
| Smart matching via description | ❌ | ❌ | ✅ |
| Project-level isolation | ❌ | ❌ | ✅ |
| One-click claude plugin commands | ❌ | ❌ | ✅ |
| Safety rules (global ops need confirmation) | ❌ | ❌ | ✅ |
| Maintained skill-catalog | ❌ | ❌ | ✅ |

---

## 解决什么问题 / Problem

Claude Code installs skills globally — all projects share them. No per-project control:
- A scraping project loads frontend skills; a frontend project loads scraping skills
- Context bloat from irrelevant skills
- Manual `claude plugin` commands are tedious

Claude Code 安装 skill 后所有项目共享，无法按项目控制：
- 爬虫项目加载前端 skill，前端项目加载爬虫 skill
- 上下文膨胀，无关 skill 干扰
- 手动管理 `claude plugin` 命令繁琐

## 方案

`project-skills-manager` 是一个 Claude Code skill，帮你自动管理：

```
你说："管理这个项目的 skills"
    ↓
skill 自动扫描项目技术栈
    ↓
推荐：哪些该添加、哪些该禁用
    ↓
你确认
    ↓
自动执行 claude plugin 命令
```

### 核心能力

| 能力 | 说明 |
|------|------|
| 技术栈诊断 | 扫描 requirements.txt、package.json 等推断项目类型 |
| 智能推荐 | 动态读取插件 description，基于关键词匹配推荐 |
| 项目级隔离 | 用 `--scope project` 管理，不影响其他项目 |
| 全局 skill 管理 | 对不需要的全局 skill 按项目禁用 |
| 安全操作 | 所有项目级操作可逆，不破坏全局配置 |

## 安装

### 方式 1：npx 安装（推荐）

```bash
npx skills add ai-x-skills/project-skills-manager -g -y
```

### 方式 2：GitHub marketplace 安装

```bash
# 添加 GitHub marketplace
claude plugin marketplace add https://github.com/ai-x-skills/project-skills-manager.git --scope user

# 安装 skill
claude plugin install project-skills-manager@project-skills-manager --scope user
```

### 方式 3：本地目录安装

```bash
# 克隆仓库
git clone https://github.com/ai-x-skills/project-skills-manager.git

# 添加为本地 marketplace
claude plugin marketplace add /path/to/project-skills-manager --scope user

# 安装 skill
claude plugin install project-skills-manager@project-skills-manager --scope user
```

### 方式 4：手动安装

将 `project-skills-manager/` 目录复制到 `~/.claude/skills/` 下。

## 使用 / Usage

Open Claude Code in any project directory, then say:

在任意项目目录下打开 Claude Code，说：

```
管理这个项目的 skills
```

or / 或：

```
这个项目需要哪些 skills？
```

### 快捷操作 / Quick Commands

| 说法 / Say | 效果 / Effect |
|------|------|
| "管理 skills" | 完整流程：诊断 → 推荐 → 确认 → 执行 / Full flow: diagnose → recommend → confirm → execute |
| "添加 xxx skill" | 直接安装到当前项目 / Install to current project directly |
| "禁用 xxx skill" | 在当前项目禁用全局 skill / Disable global skill in current project |
| "看看当前配置" | 查看已安装和已禁用的插件 / View installed and disabled plugins |
| "哪些 skill 没用" | 诊断并推荐禁用 / Diagnose and recommend disabling |

### 完整示例 / Complete Example

> User: "管理这个项目的 skills"

**Step 1: 诊断 / Diagnose**

```
项目诊断
──────────────────────
项目类型: Web 后端 API
主语言: Python
框架: FastAPI
工具: PostgreSQL, Docker, Redis
特征标签: 后端, API, 数据库

当前插件状态:
  全局启用: 12 个
  项目禁用: 0 个
  与本项目相关: ~5 个
```

**Step 2: 推荐 / Recommend**

```
推荐配置
──────────────────────

建议添加（project scope）:
  fastapi-python@npx              ← 后端框架匹配，FastAPI 最佳实践
  postgresql-database-engineering@npx  ← 数据库工具匹配

建议禁用（project scope）:
  frontend-design@anthropic-agent-skills  ← 无前端代码
  algorithmic-art@anthropic-agent-skills  ← 无创意编程需求

可保留:
  claude-api@anthropic-agent-skills  ← 项目未集成 Anthropic API，但可能有用
```

**Step 3: 确认 + 执行 / Confirm + Execute**

> User confirms → Claude executes:

```bash
claude plugin install fastapi-python@npx --scope project
claude plugin install postgresql-database-engineering@npx --scope project
claude plugin disable frontend-design@anthropic-agent-skills --scope project
claude plugin disable algorithmic-art@anthropic-agent-skills --scope project
```

**Step 4: 验证 / Verify**

```bash
claude plugin list
# Shows: fastapi-python ✅ (project), postgresql-database-engineering ✅ (project)
# Shows: frontend-design ⛔ (disabled), algorithmic-art ⛔ (disabled)
```

Other projects are completely unaffected. 其他项目完全不受影响。

---

## 工作原理 / How It Works

### 官方插件系统 / Official Plugin System

Claude Code has a complete plugin management system with three scopes:

Claude Code 有完整的插件管理系统，支持三个作用域：

| 作用域 / Scope | 命令 / Command | 效果 / Effect |
|--------|------|------|
| `user`（默认） | `claude plugin install X` | 全局可用 / Available globally |
| `project` | `claude plugin install X --scope project` | 仅当前项目 / Current project only |
| `local` | `claude plugin install X --scope local` | 仅当前会话 / Current session only |

项目级禁用存储在 `{project}/.claude/settings.json`，不影响其他项目。

Project-level disables are stored in `{project}/.claude/settings.json` and do not affect other projects.

### 推荐逻辑 / Recommendation Logic

1. Scan project files to extract language, framework, tools, and scenario features
   扫描项目文件，提取语言、框架、工具、场景等特征
2. Get available plugins from marketplace
   获取 marketplace 可用插件列表
3. Read each plugin's SKILL.md description and match keywords against project features
   读取每个插件的 SKILL.md description，用关键词与项目特征匹配
4. Classify recommendations by match strength:
   按匹配度分类推荐：
   - **强匹配 / Strong match**: description explicitly mentions project's framework/tools → recommend adding
   - **弱匹配 / Weak match**: description mentions related scenarios but no specific framework → keep
   - **不匹配 / No match**: description has no overlap with project features → recommend disabling
   - **反向匹配 / Reverse match**: project clearly lacks certain code types → recommend disabling

## 文件结构 / File Structure

```
project-skills-manager/
  .claude-plugin/
    marketplace.json           ← Marketplace 清单 / Marketplace manifest
    plugin.json                ← 插件清单 / Plugin manifest
  references/
    skill-catalog.md           ← 参考数据 / Reference data
  SKILL.md                     ← Skill 指令 / Skill instructions
  LICENSE                      ← MIT License
  README.md
```

## 前提条件 / Prerequisites

- Claude Code CLI（`claude plugin` 命令可用 / `claude plugin` commands available）

## 与其他 skill 的关系 / Related Skills

| Skill | 用途 / Purpose | 与本 skill 的关系 / Relationship |
|-------|------|------------------|
| `find-skills` | 搜索和安装新 skill / Search and install new skills | 互补：本 skill 推荐，find-skills 搜索安装 / Complementary |
| `skill-creator` | 创建新 skill / Create new skills | 无直接关系 / No direct relationship |
| `update-config` | 管理 Claude Code 配置 / Manage Claude Code config | 无直接关系 / No direct relationship |

## License

MIT — see [LICENSE](./LICENSE)
