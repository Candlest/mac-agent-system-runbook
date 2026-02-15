---
title: pi 使用 MiniMax China（minimax-cn），仅用环境变量/Keychain（不写 auth.json）
id: pi-agent-minimax-cn-env-only
tags:
  - pi
  - agent
  - minimax
  - minimax-cn
  - zsh
  - keychain
created: 2026-02-15
updated: 2026-02-15
status: stable
summary: 清理 ~/.pi/agent/auth.json 中的 minimax-cn，改为通过 zsh 从 Keychain 注入 MINIMAX_CN_API_KEY，让 pi 仅用环境变量管理 MiniMax 中国区密钥。
---

# pi 使用 MiniMax China（minimax-cn），仅用环境变量/Keychain（不写 auth.json）

## 目标

- 在 pi 中使用 MiniMax 中国区 provider（`minimax-cn`）。
- **不**在 `~/.pi/agent/auth.json` 里存 `minimax-cn` API key（避免落盘、避免优先级覆盖环境变量）。
- 用 zsh 在启动时从 macOS Keychain 读取密钥并导出 `MINIMAX_CN_API_KEY`。

## 背景

pi 支持通过环境变量提供 API key。MiniMax 中国区对应的环境变量是：

- `MINIMAX_CN_API_KEY`（provider id: `minimax-cn`）

并且 **`auth.json` 的 api key 会优先于环境变量**。因此如果你希望“只保留环境变量”，需要确保 `auth.json` 里没有 `minimax-cn`。

## 步骤

### 1) 确认/清理 `~/.pi/agent/auth.json`

检查是否存在 `minimax-cn`：

```bash
rg -n 'minimax-cn' ~/.pi/agent/auth.json
```

如存在：删除该条目（保留其他 OAuth/provider 不动）。

> 注意：不要把明文 key 写回 `auth.json`。

### 2) 把 key 存进 Keychain（如已存在可跳过）

本方案假设你已在 Keychain 存过一个条目，service 名叫 `minimax-api-key`（即便它实际是中国区 key，也没关系）。

写入/更新：

```bash
security add-generic-password -a "$USER" -s "minimax-api-key" -w "<YOUR_MINIMAX_CN_KEY>" -U
```

### 3) 在 `~/.zshrc` 注入环境变量 `MINIMAX_CN_API_KEY`

把下面片段放到 `~/.zshrc` 靠前位置（并且在 `[[ -o interactive ]] || return` 之前）：

```zsh
if command -v security >/dev/null 2>&1; then
  _mm_key="$(security find-generic-password -a "$USER" -s "minimax-api-key" -w 2>/dev/null)"
  if [[ -n "$_mm_key" ]]; then
    export MINIMAX_CN_API_KEY="$_mm_key"
  fi
  unset _mm_key
fi
```

### 4) 重新加载并启动 pi

```bash
source ~/.zshrc

echo "${MINIMAX_CN_API_KEY:+MINIMAX_CN_API_KEY is set}"
pi
```

进入 pi 后用 `/model` 选择带 `[minimax-cn]` 的模型（例如 MiniMax-M2 / MiniMax-M2.1）。

## 验证

- 环境变量存在（不回显 key）：
  ```bash
  echo "${MINIMAX_CN_API_KEY:+MINIMAX_CN_API_KEY is set}"
  ```
- `auth.json` 里不包含 minimax-cn：
  ```bash
  rg -n 'minimax-cn' ~/.pi/agent/auth.json || echo 'OK: no minimax-cn in auth.json'
  ```
- pi 中 `/model` 能看到并选择 `[minimax-cn]` 模型。

## 常见坑/排错

1) **`MINIMAX_CN_API_KEY` 为空**：
   - Keychain 里 service 名不一致（本方案用 `minimax-api-key`）。
   - 用下面命令确认是否能读到（会输出明文，仅本地确认用）：
     ```bash
     security find-generic-password -a "$USER" -s "minimax-api-key" -w
     ```

2) **明明设置了环境变量，但 pi 还是不用**：
   - 重点检查 `~/.pi/agent/auth.json` 是否存在 `minimax-cn` 的 api key 条目；存在就会覆盖 env。

3) **从 GUI 启动 pi 拿不到 env**：
   - 这是 macOS 常见行为（GUI app 不一定继承 shell env）。
   - 解决：从终端启动 `pi`，或者按需改为让 provider 直接用 `!security ...` 的命令形式（但这会变成 `models.json` 层面的配置）。

## 回滚

- 取消 `~/.zshrc` 里的 `MINIMAX_CN_API_KEY` 注入片段。
-（可选）删除 Keychain 条目：

```bash
security delete-generic-password -a "$USER" -s "minimax-api-key"
```

## 参考

- pi 文档：`docs/providers.md`（MiniMax (China) 对应 `MINIMAX_CN_API_KEY` / `minimax-cn`）
