---
title: 清理 OpenCode 对话缓存（sessions/log/tool-output）
id: opencode-cache-cleanup
tags:
  - opencode
  - cache
  - sessions
  - macos
created: 2026-02-13
updated: 2026-02-13
status: stable
summary: 定位并按需清理 OpenCode 的对话会话缓存与日志（~/.local/share/opencode），避免误删 ~/.config/opencode 配置。
---

# 清理 OpenCode 对话缓存（sessions/log/tool-output）

## 目标

- 找出 OpenCode “对话缓存/会话记录”占用空间的位置。
- 在不破坏配置的前提下按需删除。
- 删除前可先预览目录与体积，删除后可验证空间释放。

## 背景：哪些目录是配置，哪些目录是缓存？

OpenCode 常见会用两类目录：

- **配置（不要轻易删）**：`~/.config/opencode/`
  - 常见包含：`opencode.json`、instructions、skills、plugins 等
- **数据/缓存/会话（可删）**：`~/.local/share/opencode/`
  - 常见包含：
    - `storage/session*`：会话数据（session / session_share / session_diff）
    - `tool-output/`：工具执行的输出缓存
    - `log/`：日志
    - `auth.json`：认证信息（一般不建议随意删，除非你接受重新登录）

> 备注：macOS 上也可能出现 `~/Library/Application Support/opencode` 或 `~/Library/Caches/opencode`，以实际为准。

## 1) 定位目录

```sh
ls -la ~/.config/opencode 2>/dev/null || true
ls -la ~/.local/share/opencode 2>/dev/null || true
ls -la ~/Library/Application\ Support/opencode 2>/dev/null || true
ls -la ~/Library/Caches/opencode 2>/dev/null || true
```

## 2) 查看占用空间（清理前）

```sh
du -sh ~/.config/opencode 2>/dev/null || true
du -sh ~/.local/share/opencode 2>/dev/null || true
du -sh ~/Library/Application\ Support/opencode 2>/dev/null || true
du -sh ~/Library/Caches/opencode 2>/dev/null || true
```

进一步定位大头（可选）：

```sh
# 找出 share/opencode 下最占空间的一级目录
( cd ~/.local/share/opencode 2>/dev/null && du -sh * 2>/dev/null | sort -h )
```

## 3) 推荐清理策略（从“最安全”到“最彻底”）

### A. 删除 sessions + logs + tool-output（推荐）

适用：对话缓存太大，但不想影响配置/登录。

将被删除：
- `~/.local/share/opencode/storage/session`
- `~/.local/share/opencode/storage/session_share`
- `~/.local/share/opencode/storage/session_diff`
- `~/.local/share/opencode/log`
- `~/.local/share/opencode/tool-output`

命令：

```sh
rm -rf ~/.local/share/opencode/storage/session \
       ~/.local/share/opencode/storage/session_share \
       ~/.local/share/opencode/storage/session_diff \
       ~/.local/share/opencode/tool-output \
       ~/.local/share/opencode/log
```

### B. 只删除 sessions

适用：只想清空对话记录，保留日志/工具输出。

```sh
rm -rf ~/.local/share/opencode/storage/session \
       ~/.local/share/opencode/storage/session_share \
       ~/.local/share/opencode/storage/session_diff
```

### C. 只删除 log + tool-output

适用：对话还想留，但日志/工具输出太大。

```sh
rm -rf ~/.local/share/opencode/tool-output \
       ~/.local/share/opencode/log
```

### D. 全删 ~/.local/share/opencode（不推荐，最彻底）

适用：你接受“可能需要重新登录/重建本地状态”。

```sh
rm -rf ~/.local/share/opencode
```

## 4) 删除前的保险：备份一份

```sh
mkdir -p ~/Downloads/opencode-backup
cp -a ~/.local/share/opencode ~/Downloads/opencode-backup/opencode-$(date +%F) 2>/dev/null || true
```

## 5) 清理后验证

```sh
du -sh ~/.local/share/opencode 2>/dev/null || true
```

并启动 OpenCode 确认功能正常。

## 常见坑 / 排错

- **不要误删 `~/.config/opencode`**：这里是配置与脚本，删了会导致配置丢失。
- 误删/删除过头后出现“登录态丢失”：通常是 `auth.json` 被删了；重新登录即可。
- 想保留少量历史：用 `find ... -mtime +N -delete` 做“按天清理”（需要先确认文件结构，避免误删）。
