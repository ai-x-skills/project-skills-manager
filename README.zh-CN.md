# Project Skills Manager

[English](./README.md)

> 智能管理 Claude Code 项目级 Skill 配置。分析项目技术栈，推荐相关 skill，一键配置。

## 有什么不同

| | 手动管理 | Claude 内置提示 | **project-skills-manager** |
|---|:-:|:-:|:-:|
| 自动扫描项目技术栈 | ❌ | ❌ | ✅ |
| 基于 description 智能匹配推荐 | ❌ | ❌ | ✅ |
| 项目级隔离，互不影响 | ❌ | ❌ | ✅ |
| 一键执行 claude plugin 命令 | ❌ | ❌ | ✅ |
| 安全规则（全局操作需确认） | ❌ | ❌ | ✅ |
| 维护 skill-catalog 持续更新 | ❌ | ❌ | ✅ |

---

## 解决什么问题

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

## 使用

在任意项目目录下打开 Claude Code，说：

```
管理这个项目的 skills
```

或：

```
这个项目需要哪些 skills？
```

### 快捷操作

| 说法 | 效果 |
|------|------|
| "管理 skills" | 完整流程：诊断 → 推荐 → 确认 → 执行 |
| "添加 xxx skill" | 直接安装到当前项目 |
| "禁用 xxx skill" | 在当前项目禁用全局 skill |
| "看看当前配置" | 查看已安装和已禁用的插件 |
| "哪些 skill 没用" | 诊断并推荐禁用 |

### 完整示例

> 用户："管理这个项目的 skills"

**Step 1: 诊断**

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

**Step 2: 推荐**

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

**Step 3: 确认 + 执行**

> 用户确认 → Claude 执行：

```bash
claude plugin install fastapi-python@npx --scope project
claude plugin install postgresql-database-engineering@npx --scope project
claude plugin disable frontend-design@anthropic-agent-skills --scope project
claude plugin disable algorithmic-art@anthropic-agent-skills --scope project
```

**Step 4: 验证**

```bash
claude plugin list
# Shows: fastapi-python ✅ (project), postgresql-database-engineering ✅ (project)
# Shows: frontend-design ⛔ (disabled), algorithmic-art ⛔ (disabled)
```

其他项目完全不受影响。

---

## 工作原理

### 官方插件系统

Claude Code 有完整的插件管理系统，支持三个作用域：

| 作用域 | 命令 | 效果 |
|--------|------|------|
| `user`（默认） | `claude plugin install X` | 全局可用 |
| `project` | `claude plugin install X --scope project` | 仅当前项目 |
| `local` | `claude plugin install X --scope local` | 仅当前会话 |

项目级禁用存储在 `{project}/.claude/settings.json`，不影响其他项目。

### 推荐逻辑

1. 扫描项目文件，提取语言、框架、工具、场景等特征
2. 获取 marketplace 可用插件列表
3. 读取每个插件的 SKILL.md description，用关键词与项目特征匹配
4. 按匹配度分类推荐：
   - **强匹配**：description 明确提到项目的框架/工具 → 建议添加
   - **弱匹配**：相关场景，但无具体框架 → 保留
   - **不匹配**：无交集 → 建议禁用
   - **反向匹配**：项目明显缺少某类代码 → 建议禁用

## 文件结构

```
project-skills-manager/
  .claude-plugin/
    marketplace.json           ← Marketplace 清单
    plugin.json                ← 插件清单
  references/
    skill-catalog.md           ← 参考数据
  SKILL.md                     ← Skill 指令
  LICENSE                      ← MIT License
  README.md
  README.zh-CN.md
```

## 前提条件

- Claude Code CLI（`claude plugin` 命令可用）

## 与其他 skill 的关系

| Skill | 用途 | 与本 skill 的关系 |
|-------|------|------------------|
| `find-skills` | 搜索和安装新 skill | 互补 |
| `skill-creator` | 创建新 skill | 无直接关系 |
| `update-config` | 管理 Claude Code 配置 | 无直接关系 |

## 许可证

MIT — 见 [LICENSE](./LICENSE)
