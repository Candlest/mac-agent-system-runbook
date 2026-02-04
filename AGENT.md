---
title: mac-runbook 写作/生成规范
id: mac-runbook-agent
tags:
  - runbook
  - writing
  - opencode
created: 2026-02-03
updated: 2026-02-04
status: stable
summary: 记录在 mac-runbook 下生成/维护文档时的约定与工作流。
---

# AGENT.md

本文件定义 `~/Documents/mac-runbook` 的写作规范与维护流程，用于把“系统维护/工具链配置”的重复劳动沉淀成可检索的手册。

## 核心原则

- 解决一个明确问题：一篇文档只解决一个主题/场景
- 可执行：给出可复制的命令/步骤 + 明确的验证方法
- 可回滚：涉及改动时写清回滚/恢复步骤
- 可索引：统一 frontmatter，便于规模化检索

## Frontmatter 规范

每个 Markdown 文件必须以 YAML frontmatter 开头：

- 必填字段：
  - `title`: 人类可读标题
  - `id`: 全局唯一（建议 `kebab-case`）
  - `tags`: 主题标签数组（小而准）
  - `created` / `updated`: `YYYY-MM-DD`
  - `status`: `stable` | `draft` | `deprecated`
  - `summary`: 1 行摘要（用于快速索引）
- 可选字段：
  - `source`: 外部来源链接
  - `superseded_by`: 新文档文件名（当 `status: deprecated` 时必填）

## 文件命名

- 默认：`主题-场景.md`（例：`zsh-fishlike.md`）
- 合并后的主文档可用更短名称（例：`musicfox.md`）

## 文档结构（推荐）

- 简介/目标
- 前置条件（版本/系统/依赖）
- 步骤（按顺序，可复制）
- 验证（如何确认成功）
- 排错（常见失败与定位）
- 回滚（如何恢复到改动前）
- 参考链接（可选）

## 合并与弃用（简并同类内容）

- 同主题多份文档：合并为一个主文档
- 被合并的旧文档：保留为“入口页”，内容只保留指向主文档的说明
  - frontmatter 设置 `status: deprecated`
  - 填写 `superseded_by: <newfile>.md`

## 更新流程

- 修改任意文档后，更新其 `updated` 日期
- 如果新增文档值得常用：把它加入 `README.md` 的“快速入口”
