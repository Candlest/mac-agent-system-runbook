---
title: mac-agent-system-runbook
id: mac-agent-system-runbook
tags:
  - macos
  - runbook
  - system-maintenance
created: 2026-02-04
updated: 2026-02-04
status: stable
summary: macOS 系统维护与工具链配置的可检索手册库。
---

# mac-agent-system-runbook

把零散的系统维护知识沉淀为可检索的手册，减少"脏活（dirty work）"在日常系统维护中的占比，让使用者把时间更多花在具体业务上。

## 适用范围

- macOS 机器的日常维护与开发环境维护
- Shell/终端/工具链（zsh、starship、mise、pnpm、uv、micromamba 等）
- 常用软件与快捷键整理
- OpenCode agent 配置与自动化任务

## 目录约定

- 文档统一放在本目录下（Markdown）
- 文件命名：`主题-场景.md`（例：`zsh-fishlike.md`）
- 一篇文档尽量解决一个明确问题；包含：背景、做法、验证、回滚/排错

## Frontmatter 约定

每个 Markdown 文件顶部都带 YAML frontmatter，便于后续批量索引/检索：

- 必填：`title`、`id`、`tags`、`created`、`updated`、`status`、`summary`
- 可选：`source`（来源链接）、`superseded_by`（被哪个新文档取代）

## 快速入口

- [zsh-fishlike](./zsh-fishlike.md)：zsh 中获得接近 fish 的补全/高亮体验（含补全更新方式）
- [musicfox](./musicfox.md)：go-musicfox 安装、配置与快捷键
- [opencode-skill-global](./opencode-skill-global.md)：OpenCode 全局 skill 创建与注册（含脚本与注意点）
- [AGENT](./AGENT.md)：OpenCode agent 配置与指令集

## 维护方式

- 新增/更新文档：直接在本目录增补 Markdown
- 遇到反复出现的问题：优先写进文档，再把链接/关键词补充到全局 OpenCode agent 配置，便于自动/被动查阅
