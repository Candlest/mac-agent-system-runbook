---
title: mac-agent-system-runbook
id: mac-agent-system-runbook
tags:
  - macos
  - runbook
  - system-maintenance
created: 2026-02-04
updated: 2026-02-13
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

## 文档规范

- 文件命名：`主题-场景.md`（例：`zsh-fishlike.md`）
- 每个 Markdown 文件顶部带 YAML frontmatter，便于索引/检索
- 必填字段：`title`、`id`、`tags`、`created`、`updated`、`status`、`summary`
- 一篇文档尽量解决一个明确问题，包含：背景、做法、验证、回滚/排错

## 目录结构

### Shell 与终端

| 文档 | 描述 |
|------|------|
| [zsh-fishlike](./zsh-fishlike.md) | zsh 中获得接近 fish 的补全/高亮体验 |
| [pi-agent-glm-minimax-keychain](./pi-agent-glm-minimax-keychain.md) | pi agent 配置 GLM + MiniMax，并用 Keychain 管理 API key |
| [pi-agent-minimax-cn-env-only](./pi-agent-minimax-cn-env-only.md) | pi 仅用环境变量/Keychain 使用 MiniMax 中国区（minimax-cn），不写 auth.json |
| [pi-agent-index](./pi-agent-index.md) | pi agent 相关 runbook 索引与配置要点汇总 |

### 网络代理

| 文档 | 描述 |
|------|------|
| [clash-verge-config](./clash-verge-config.md) | Clash Verge 代理配置与节点组管理 |

### 桌面应用

| 文档 | 描述 |
|------|------|
| [musicfox](./musicfox.md) | go-musicfox 安装、配置与快捷键 |
| [vscode-code-command-reset](./vscode-code-command-reset.md) | VS Code `code` 命令重启失效修复 |

### OpenCode 核心配置

| 文档 | 描述 |
|------|------|
| [AGENT](./AGENT.md) | Runbook 写作规范与工作流 |
| [opencode-api-provider-guide](./opencode-api-provider-guide.md) | API 供应商和模型配置 |
| [opencode-mcp-config](./opencode-mcp-config.md) | MCP 服务器默认禁用与按需开启 |
| [opencode-skill-global](./opencode-skill-global.md) | 全局 Skill 创建与注册 |
| [opencode-cache-cleanup](./opencode-cache-cleanup.md) | 清理 OpenCode 对话缓存与日志（sessions/log/tool-output） |

### OpenCode Skills

| 文档 | 描述 |
|------|------|
| [superpowers-skill](./superpowers-skill.md) | Superpowers 强制性软件开发工作流框架（13个Skills） |
| [ui-ux-pro-max-skill](./ui-ux-pro-max-skill.md) | UI/UX 设计系统 Skill（67种样式、96种配色） |

### MCP 服务器

| 文档 | 描述 |
|------|------|
| [chrome-devtools-mcp](./chrome-devtools-mcp.md) | Chrome DevTools MCP 配置与维护 |

## 快速入口

**日常开发**
- [Superpowers 工作流](./superpowers-skill.md) - 强制 TDD + 系统化调试
- [UI UX Pro Max](./ui-ux-pro-max-skill.md) - 生成设计系统

**终端配置**
- [zsh 体验优化](./zsh-fishlike.md)
- [musicfox 音乐播放器](./musicfox.md)

**网络配置**
- [Clash Verge 代理配置](./clash-verge-config.md)

**OpenCode 配置**
- [API 供应商配置](./opencode-api-provider-guide.md)
- [MCP 服务器管理](./opencode-mcp-config.md)
- [全局 Skills](./opencode-skill-global.md)

**工具故障**
- [VS Code code 命令修复](./vscode-code-command-reset.md)
- [Chrome DevTools MCP](./chrome-devtools-mcp.md)

## 维护方式

- 新增/更新文档：直接在本目录增补 Markdown
- 遇到反复出现的问题：优先写进文档，再把链接/关键词补充到全局 OpenCode agent 配置，便于自动/被动查阅
