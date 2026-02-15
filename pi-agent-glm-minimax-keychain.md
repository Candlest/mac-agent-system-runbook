---
title: pi agent 配置 GLM + MiniMax（Keychain 密钥管理）
id: pi-agent-glm-minimax-keychain
tags:
  - pi
  - agent
  - llm
  - minimax
  - glm
  - keychain
  - zsh
created: 2026-02-13
updated: 2026-02-13
status: stable
summary: 为 pi agent 增加 GLM / minimaxi.com 模型，并用 macOS Keychain 管理 API key，避免明文写入 dotfiles。
---

# pi agent 配置 GLM + MiniMax（Keychain 密钥管理）

## 目标

- 在 pi agent 中增加自定义 provider：GLM、MiniMax（minimaxi.com）。
- API key 不落盘到 `~/.zshrc`/`~/.zshenv`（避免明文泄露），改为存入 macOS Keychain，并在 shell 启动时读取。

## 背景与约定

- pi agent 的自定义模型文件：`~/.pi/agent/models.json`（见 pi 文档 `docs/models.md`：Custom Models）。
- 环境变量约定：
  - `GLM_API_KEY`
  - `MINIMAX_API_KEY`
- Keychain 条目约定（Generic Password）：
  - service: `glm-api-key`
  - service: `minimax-api-key`
  - account: `$USER`

## 1. 配置 pi agent providers/models

编辑/创建：`~/.pi/agent/models.json`

示例（按需调整 model id / context window / maxTokens）：

```json
{
  "$schema": "https://pi.ai/models.schema.json",
  "providers": {
    "glm": {
      "baseUrl": "https://open.bigmodel.cn/api/paas/v4",
      "api": "openai-completions",
      "apiKey": "GLM_API_KEY",
      "authHeader": true,
      "models": [
        { "id": "glm-4.5", "name": "GLM-4.5", "reasoning": true },
        { "id": "glm-4-plus", "name": "GLM-4-Plus" }
      ]
    },
    "minimax-com": {
      "baseUrl": "https://api.minimaxi.com/v1",
      "api": "openai-completions",
      "apiKey": "!security find-generic-password -a $(whoami) -s minimax-api-key -w",
      "authHeader": true,
      "models": [
        { "id": "MiniMax-M2.5", "name": "MiniMax M2.5 (minimaxi.com)", "reasoning": true },
        { "id": "MiniMax-M2.1", "name": "MiniMax M2.1 (minimaxi.com)", "reasoning": true }
      ]
    }
  }
}
```

*pi agent 构造 minimaxi.com 请求时会执行 `security find-generic-password -a $(whoami) -s minimax-api-key -w` 读取 Keychain，确保即便直接从 macOS UI 启动 pi（未 source dotfiles），也能在 Authorization 头里带上 `Bearer <密钥>`。首次运行会弹窗要求允许访问，之后会记住。*

### 验证

在 pi 中打开模型选择器 `/model`，应能看到新 provider 下的模型。

## 2. 把 API key 存入 macOS Keychain

写入/更新（`-U` 表示存在则更新）：

```sh
security add-generic-password -a "$USER" -s "minimax-api-key" -w "<YOUR_MINIMAX_KEY>" -U
security add-generic-password -a "$USER" -s "glm-api-key" -w "<YOUR_GLM_KEY>" -U
```

读取验证（会输出明文 key，本地自用）：

```sh
security find-generic-password -a "$USER" -s "minimax-api-key" -w
security find-generic-password -a "$USER" -s "glm-api-key" -w
```

> 注意：命令里必须使用英文直引号 `"`，不要用中文弯引号 `“ ”`，否则写入内容可能不符合预期。

## 3. 在 ~/.zshrc 中启动时自动读取 Keychain

把以下片段加入 `~/.zshrc`（建议放在靠前位置，且在 `[[ -o interactive ]] || return` 之前）：

```sh
# ---------- Secrets (macOS Keychain) ----------
# Store with (examples):
#   security add-generic-password -a "$USER" -s "minimax-api-key" -w "..." -U
#   security add-generic-password -a "$USER" -s "glm-api-key" -w "..." -U
# Read (no echo):
#   security find-generic-password -a "$USER" -s "minimax-api-key" -w
#   security find-generic-password -a "$USER" -s "glm-api-key" -w
#
# Only set variables if keychain entries exist
if command -v security >/dev/null 2>&1; then
  _mm_key="$(security find-generic-password -a "$USER" -s "minimax-api-key" -w 2>/dev/null)"
  if [[ -n "$_mm_key" ]]; then
    export MINIMAX_API_KEY="$_mm_key"
  fi
  unset _mm_key

  _glm_key="$(security find-generic-password -a "$USER" -s "glm-api-key" -w 2>/dev/null)"
  if [[ -n "$_glm_key" ]]; then
    export GLM_API_KEY="$_glm_key"
  fi
  unset _glm_key
fi
```

### 验证（不回显 key）

```sh
source ~/.zshrc

echo "${MINIMAX_API_KEY:+MINIMAX_API_KEY is set}"
echo "${GLM_API_KEY:+GLM_API_KEY is set}"
```

说明：`${VAR:+...}` 是 zsh/bash 参数展开语法：变量存在且非空时输出提示，否则输出空。

## 4. 清理：不要在 ~/.zshenv / ~/.zshrc 明文保存 key

检查这些文件中是否存在明文：

- `~/.zshenv`
- `~/.zshrc`
- `~/.zshenv.secrets`（如果你曾经用过文件存储方案）

建议：`~/.zshenv` 尽量只放“无敏感信息”的环境设置。

## 5. 常见问题排查

### 5.1 `echo "${MINIMAX_API_KEY:+MINIMAX_API_KEY is set}"` 输出了提示，是不是异常？
不是异常。表示变量已成功设置。

### 5.2 为什么要放在 `[[ -o interactive ]] || return` 之前？
很多人会在 `~/.zshrc` 里对非交互 shell 直接 `return`。如果密钥加载逻辑放在后面，非交互场景（例如某些工具启动子 shell）可能拿不到环境变量。

### 5.3 Keychain 读取会弹窗吗？
首次读取或权限变动时，macOS 可能弹出 Keychain 访问确认。

## 回滚

- 删除 Keychain 条目：

```sh
security delete-generic-password -a "$USER" -s "minimax-api-key"
security delete-generic-password -a "$USER" -s "glm-api-key"
```

- 从 `~/.zshrc` 移除 Keychain 读取片段。
