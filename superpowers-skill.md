---
title: Superpowers Skill 安装与配置
id: superpowers-skill
tags:
  - opencode
  - skill
  - software-engineering
  - tdd
  - workflow
created: 2026-02-08
updated: 2026-02-08
status: stable
summary: 安装 Superpowers - 强制性软件开发工作流框架,包含 TDD、系统化调试和子代理驱动开发
---

# Superpowers Skill 安装与配置

## 目标

安装 Superpowers 到 OpenCode 全局环境,为 AI Agent 提供强制性的软件开发工作流,包含严格的 TDD 流程、系统化调试、子代理驱动开发等 13 个 skills。

## Superpowers 是什么

Superpowers 是一个完整的软件开发工作流框架,通过可组合的 "skills" 让 AI Agent 自动遵循软件工程最佳实践。

**核心特性:**
- **强制性工作流**: 不是建议,是必须执行的流程
- **自动触发**: Agent 在每个任务前自动检查相关 skills
- **13 个 Skills**: 覆盖测试、调试、协作、元技能
- **子代理驱动**: 每个任务分派独立子代理,可自主工作数小时

**关键原则:**
- **Test-Driven Development (TDD)**: 测试先行,永远
- **Systematic over ad-hoc**: 流程优先于猜测
- **Complexity reduction**: 简单性是首要目标
- **Evidence over claims**: 验证后再宣布成功

## 前置条件

- [x] OpenCode 已安装
- [x] Git 已安装
- [x] ~/.config/opencode/ 目录存在

## 安装步骤

### 1. 克隆 Superpowers 仓库

```bash
git clone https://github.com/obra/superpowers.git ~/.config/opencode/superpowers
```

### 2. 创建目录

```bash
mkdir -p ~/.config/opencode/plugins ~/.config/opencode/skills
```

### 3. 创建 Symlinks

**Plugin symlink:**
```bash
ln -s ~/.config/opencode/superpowers/.opencode/plugins/superpowers.js \
      ~/.config/opencode/plugins/superpowers.js
```

**Skills symlink:**
```bash
ln -s ~/.config/opencode/superpowers/skills \
      ~/.config/opencode/skills/superpowers
```

### 4. 验证安装

```bash
# 检查 plugin symlink
ls -l ~/.config/opencode/plugins/superpowers.js
# 应显示指向: ~/.config/opencode/superpowers/.opencode/plugins/superpowers.js

# 检查 skills symlink
ls -l ~/.config/opencode/skills/superpowers
# 应显示指向: ~/.config/opencode/superpowers/skills

# 列出所有 skills
ls ~/.config/opencode/superpowers/skills/
```

### 5. 重启 OpenCode

让新安装的 plugin 和 skills 生效。

## 可用 Skills

### 核心工作流 (7 步)

1. **brainstorming** - 头脑风暴
   - 触发时机: 在写代码前
   - 功能: 苏格拉底式提问细化需求,分段展示设计

2. **using-git-worktrees** - Git 工作树
   - 触发时机: 设计批准后
   - 功能: 创建隔离工作空间,新分支,验证测试基线

3. **writing-plans** - 编写计划
   - 触发时机: 设计批准后
   - 功能: 拆解为 2-5 分钟的小任务,每任务包含完整代码和验证步骤

4. **subagent-driven-development** - 子代理驱动开发
   - 触发时机: 有实施计划时
   - 功能: 为每个任务分派独立子代理,两阶段审查

5. **test-driven-development** - 测试驱动开发
   - 触发时机: 实施任何功能或修复 bug 时
   - 功能: 强制 RED-GREEN-REFACTOR 循环

6. **requesting-code-review** - 请求代码审查
   - 触发时机: 任务之间
   - 功能: 对照计划审查,按严重性报告问题

7. **finishing-a-development-branch** - 完成开发分支
   - 触发时机: 所有任务完成时
   - 功能: 验证测试,提供 merge/PR/keep/discard 选项

### 测试技能

- **test-driven-development** - RED-GREEN-REFACTOR 循环
- **testing-anti-patterns** - 测试反模式参考(被 TDD skill 引用)

### 调试技能

- **systematic-debugging** - 4 阶段根因分析流程
- **verification-before-completion** - 确保问题真正解决
- **root-cause-tracing** - 根因追踪技术
- **defense-in-depth** - 深度防御
- **condition-based-waiting** - 条件等待

### 协作技能

- **executing-plans** - 批量执行,带人工检查点
- **dispatching-parallel-agents** - 并发子代理工作流
- **receiving-code-review** - 响应反馈

### 元技能

- **using-superpowers** - 技能系统介绍和 bootstrap
- **writing-skills** - 创建新 skill 的最佳实践

## 核心工作流详解

### 完整开发流程

```
1. brainstorming (理解需求 → 设计)
   ↓
2. using-git-worktrees (创建隔离工作空间)
   ↓
3. writing-plans (拆解为小任务)
   ↓
4. subagent-driven-development (子代理执行任务)
   ↓
5. test-driven-development (每个任务强制 TDD)
   ↓
6. requesting-code-review (任务之间审查)
   ↓
7. finishing-a-development-branch (完成分支)
```

### TDD 铁律

**核心原则:**
```
没有失败测试先,不写生产代码
```

**RED-GREEN-REFACTOR 循环:**

1. **RED** - 写失败测试
   - 一个测试只测一个行为
   - 名字清晰,描述期望行为
   - 使用真实代码,避免 mock

2. **Verify RED** - 看它失败
   - **必须执行,不能跳过**
   - 确认测试失败(不是错误)
   - 确认失败原因是功能缺失(不是拼写错误)

3. **GREEN** - 写最小代码
   - 最简单代码通过测试
   - 不添加额外功能
   - 不重构其他代码

4. **Verify GREEN** - 看它通过
   - **必须执行,不能跳过**
   - 确认测试通过
   - 确认其他测试仍然通过

5. **REFACTOR** - 清理
   - 删除重复
   - 改进命名
   - 提取助手函数
   - 保持测试通过

**反合理化规则:**

| 借口 | 现实 |
|------|------|
| "太简单不需要测试" | 简单代码也会出错 |
| "先写代码,之后再测试" | 之后通过的测试什么都证明不了 |
| "我已经手动测试了" | 临时测试 ≠ 系统化测试 |
| "删除代码太浪费" | 沉没成本谬误,保留未验证代码是技术债 |
| "TDD 太教条" | TDD 才是务实,更早发现 bug |

**删除原则:**
- 写测试前的代码 → 删除,重新开始
- 不是 "保留作为参考"
- 不是 "边写测试边改代码"

## 使用方法

### 自动触发

Superpowers 通过 `using-superpowers` skill 自动注入到 OpenCode,Agent 会:
- 在每个任务前检查相关 skills
- 自动触发适用的 skills
- 不需要手动调用

### 手动触发 (如果需要)

使用 OpenCode 的 `skill` 工具:

```
use skill tool to load superpowers/brainstorming
```

### Skill 优先级

当多个 skills 适用时:
1. **Process skills first** (brainstorming, debugging) - 决定 **如何** 处理
2. **Implementation skills second** (具体领域 skills) - 指导执行

### 工具映射 (OpenCode 适配)

Superpowers 原为 Claude Code 设计,OpenCode 会自动映射:

| Claude Code Tool | OpenCode 等效工具 |
|------------------|-------------------|
| `Skill` tool | `skill` tool (native) |
| `TodoWrite` | `update_plan` |
| `Task` (subagents) | `@mention` 系统 |
| `Read/Write/Edit` | 原生工具 |

## 自定义 Skills

### 创建个人 Skills

在 `~/.config/opencode/skills/` 创建自己的 skills:

```bash
mkdir -p ~/.config/opencode/skills/my-skill
```

创建 `~/.config/opencode/skills/my-skill/SKILL.md`:

```markdown
---
name: my-skill
description: 触发条件 - 功能描述
---

# My Skill

[你的 skill 内容]
```

### 创建项目 Skills

在项目根目录 `.opencode/skills/` 创建项目特定 skills:

```bash
mkdir -p .opencode/skills/my-project-skill
```

### Skill 优先级

1. **Project skills** (`.opencode/skills/`) - 最高优先级
2. **Personal skills** (`~/.config/opencode/skills/`)
3. **Superpowers skills** (`~/.config/opencode/skills/superpowers/`)

## 集成本地工具链规范

### 结合 Runbook

Superpowers 可以调用你的 Runbook 文档:

- **mac-agent-system-runbook**: 系统维护和工具链配置
- **ui-ux-pro-max-skill**: UI/UX 设计系统

示例工作流:
```
用户: "Build a landing page for beauty spa"
→ Agent 调用 brainstorming
→ Agent 检测到 "landing page" 触发 ui-ux-pro-max
→ 生成设计系统
→ 使用 writing-plans 拆解任务
→ 子代理执行,强制 TDD
```

### Python 虚拟环境

Superpowers 不强制 Python 虚拟环境,但你的环境规范要求:
- 所有 Python 项目使用 uv 管理
- 优先使用虚拟环境而非全局 Python

可以在自定义 skill 中强制执行此规范。

### 运行时管理

Superpowers 不强制使用 mise,但你可以:
- 在 `writing-plans` 阶段指定运行时版本
- 在项目级 skills 中添加 mise 检查

## 更新

```bash
cd ~/.config/opencode/superpowers
git pull
```

重启 OpenCode 加载更新。

## 故障排除

### Plugin 未加载

1. 检查 plugin symlink:
   ```bash
   ls -l ~/.config/opencode/plugins/superpowers.js
   ```
2. 检查 plugin 文件存在:
   ```bash
   ls ~/.config/opencode/superpowers/.opencode/plugins/superpowers.js
   ```
3. 查看 OpenCode 日志:
   ```bash
   opencode run "test" --print-logs --log-level DEBUG
   ```
4. 查找 plugin 加载消息

### Skills 未找到

1. 验证 skills symlink:
   ```bash
   ls -l ~/.config/opencode/skills/superpowers
   ```
2. 应指向: `~/.config/opencode/superpowers/skills/`
3. 使用 `skill` 工具列出可用 skills:
   ```
   use skill tool to list skills
   ```

### Bootstrap 未出现

1. 验证 using-superpowers skill 存在:
   ```bash
   ls ~/.config/opencode/superpowers/skills/using-superpowers/SKILL.md
   ```
2. 检查 OpenCode 版本是否支持 `experimental.chat.system.transform` hook
3. 重启 OpenCode

### 测试安装

```bash
# 测试 bootstrap 注入
opencode run "what superpowers do you have?"

# 测试 skills 发现
opencode run "use skill tool to list all skills"

# 查找 superpowers 提及
opencode run --print-logs "hello" 2>&1 | grep -i superpowers
```

Agent 应该:
- 提及有 superpowers
- 能够列出 `superpowers/` 下的 skills

## 与 UI UX Pro Max 集成

### 集成方案

UI UX Pro Max 可以在 Superpowers 工作流中作为设计阶段工具:

**Phase 1: Brainstorming**
```
用户: "Build a beauty spa website"
→ brainstorming skill 激活
→ 理解需求
→ 调用 ui-ux-pro-max 生成设计系统
→ 用户确认设计
```

**Phase 2: Planning**
```
→ writing-plans skill 激活
→ 拆解任务,包含设计系统规范
```

**Phase 3: Implementation**
```
→ subagent-driven-development + TDD
→ 每个任务遵循设计系统
```

### 触发 UI UX Pro Max

在自定义 skill 中添加触发规则:

```markdown
---
name: design-system-integration
description: Use when brainstorming or designing - automatically generates design systems using UI UX Pro Max
---

# Design System Integration

当检测到以下关键词时,调用 UI UX Pro Max:
- "landing page"
- "website"
- "design"
- "UI/UX"
- "界面"

## 触发命令

~/.config/opencode/.opencode/skills/ui-ux-pro-max/scripts/search.py \
  "<product_type> <keywords>" --design-system -p "ProjectName"
```

## 资源

- **GitHub**: https://github.com/obra/superpowers
- **Issues**: https://github.com/obra/superpowers/issues
- **博客**: https://blog.fsck.com/2025/10/09/superpowers/
- **Marketplace**: https://github.com/obra/superpowers-marketplace

## 变更日志

- **2026-02-08**: 初始安装到 OpenCode,验证 skills 和 plugin 正常工作
- **2026-02-08**: 创建 Runbook 文档,记录安装流程和使用方法
