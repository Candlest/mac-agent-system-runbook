---
title: pi agent 文档索引与配置要点
id: pi-agent-index
tags:
  - pi
  - agent
  - llm
  - providers
  - keychain
  - zsh
created: 2026-02-15
updated: 2026-02-15
status: stable
summary: 汇总本仓库所有 pi agent 相关 runbook，并提炼关键配置点（环境变量 vs auth.json 优先级、Keychain 注入、models.json 位置与验证）。
---

# pi agent 文档索引与配置要点

## 目标

- 给本仓库的 **pi（pi-coding-agent）** 相关文档提供一个可检索入口。
- 把最容易忘/最常踩坑的配置点集中在一页：
  - API key：环境变量、`~/.pi/agent/auth.json`、Keychain 的优先级与使用方式
  - 自定义模型与 provider：`~/.pi/agent/models.json`
  - 验证方法与回滚方式

## 文档索引（本仓库）

### 供应商 / 模型 / 密钥管理

- [pi-agent-glm-minimax-keychain](./pi-agent-glm-minimax-keychain.md)
  - 自定义 provider（GLM、minimaxi.com）示例
  - Keychain 读写命令
  - 在 `~/.zshrc` 中把 Keychain 注入为环境变量

- [pi-agent-minimax-cn-env-only](./pi-agent-minimax-cn-env-only.md)
  - 使用内置 provider：`minimax-cn`
  - 仅使用 `MINIMAX_CN_API_KEY`（Keychain→zsh→env）
  - 清理 `auth.json` 中会覆盖 env 的 `minimax-cn` 条目

## 关键配置点速记

### 1) API key 的解析优先级（容易踩坑）

- `~/.pi/agent/auth.json` 里的 **api key**（若存在）通常会 **优先于**环境变量。
- 如果你的目标是“只靠环境变量管理 key”，就要确保 `auth.json` 中 **不要**存在对应 provider 的 api key 条目。

快速检查（示例以 minimax-cn 为例）：

```bash
rg -n 'minimax-cn' ~/.pi/agent/auth.json || echo 'OK: no minimax-cn in auth.json'
```

### 2) 推荐：Keychain 存储 + zsh 注入 env（避免明文落盘）

- Keychain 负责存储密钥（不写进 dotfiles/仓库）。
- `~/.zshrc` 在启动时读取 Keychain 并 `export XXX_API_KEY=...`。

优点：
- `pi` 从终端启动时直接可用。
- 不需要把 key 写到 `~/.pi/agent/auth.json`。

注意：
- 从 GUI 启动的程序未必继承 shell 环境变量；这种场景可改用 provider 的 `!security ...` 命令形式（见相关 runbook），或坚持从终端启动 `pi`。

### 3) 自定义 provider / 模型位置

- 自定义 provider / 模型：`~/.pi/agent/models.json`
- 会话与凭据：`~/.pi/agent/sessions/`、`~/.pi/agent/auth.json`

### 4) 常用验证步骤（不泄露明文 key）

```bash
# 检查变量是否已设置（不输出 key）
echo "${MINIMAX_CN_API_KEY:+MINIMAX_CN_API_KEY is set}"
echo "${GLM_API_KEY:+GLM_API_KEY is set}"

# 启动并在 pi 内选择模型
pi
# /model
```

## 回滚思路

- 若改动涉及 `~/.zshrc`：移除对应 export/Keychain 读取片段即可。
- 若误把 api key 写进 `~/.pi/agent/auth.json`：删除对应 provider 条目，然后重新启动 `pi`。
- 若要彻底移除 Keychain 中的条目：使用 `security delete-generic-password ...`（具体命令见各子文档）。
