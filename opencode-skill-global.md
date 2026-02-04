---
title: OpenCode 全局 Skill 创建与注册
id: opencode-skill-global
tags:
  - opencode
  - skill
  - cli
created: 2026-02-04
updated: 2026-02-04
status: stable
summary: 记录全局 skills 的目录结构、SKILL.md 规范、脚本放置与刷新方式。
---

# OpenCode 全局 Skill 创建与注册

## 目标

在本机创建可复用的全局 skill，并让 OpenCode 在 skills 列表中可见。

## 前置条件

- OpenCode 已安装
- 全局技能目录存在：`~/.claude/skills/`

## 步骤

1. 创建 skill 目录

```bash
mkdir -p ~/.claude/skills/<skill-name>
```

2. 编写 `SKILL.md`（必须包含 YAML frontmatter）

```md
---
name: <skill-name>
description: <一句话描述触发场景和用途>
version: 1.0
---

# <skill-name>
```

3. （可选）放置脚本或资源文件

- 例如：`~/.claude/skills/<skill-name>/<skill-name>`
- 需要执行权限时运行：

```bash
chmod +x ~/.claude/skills/<skill-name>/<script>
```

4. 刷新 skills 列表

- 重新启动 OpenCode
- 或执行 OpenCode 的“刷新/重载 skills”命令（不同版本命令不同）

## 注意点

- `SKILL.md` 必须有 frontmatter，否则不会出现在 skills 列表
- `description` 决定技能触发时机，尽量明确关键词
- 秘钥不要写进 `SKILL.md` 或脚本，放在 `~/.zshenv` 或其他环境变量文件
- 如果输出依赖 `jq`/`python` 等工具，请在文档中写明依赖

## 验证

- 运行你的 skills 列表命令，确认 `<skill-name>` 出现
- 运行脚本或示例命令，确认输出正常

## 回滚

```bash
rm -rf ~/.claude/skills/<skill-name>
```
