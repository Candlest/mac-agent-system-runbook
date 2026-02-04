---
title: go-musicfox
id: musicfox
tags:
  - cli
  - netease
  - music
  - macos
created: 2026-02-03
updated: 2026-02-04
status: stable
summary: go-musicfox（musicfox）在 macOS 上的安装、使用、配置与快捷键。
source: https://github.com/go-musicfox/go-musicfox
---

# go-musicfox（musicfox）

## 简介

go-musicfox 是用 Go 语言编写的网易云音乐命令行客户端，支持多种平台和各种功能特性。

## 功能特性

- 支持 UnblockNeteaseMusic
- 多种音质级别选择
- Last.fm 集成
- macOS 交互响应（睡眠暂停、蓝牙耳机连接断开响应、菜单栏控制等）
- 歌词显示
- 音乐下载

## 安装

### macOS（推荐）

Homebrew：

```bash
brew install anhoder/go-musicfox/go-musicfox
```

如果之前安装过旧的 `musicfox`，需要重新链接：

```bash
brew unlink musicfox && brew link --overwrite go-musicfox
```

直接下载：从 Release 下载 macOS 可执行文件。

### 其他平台

本仓库以 macOS 使用为主；Linux/Windows 的安装与编译方式请以官方文档为准：

- https://github.com/go-musicfox/go-musicfox

## 使用

启动：

```bash
musicfox
```

### 注意事项

- 请务必使用等宽字体，或将配置项 `doubleColumn` 设为 `false`
- 如出现光标移动异常，请将 `enableMouseEvent` 设置为 `false`
- macOS 推荐使用 iTerm2 或 Kitty

## 快捷键

### 基础导航（不可自定义）

| 按键 | 功能 |
|------|------|
| `h` / `H` / `Left` | 左移 |
| `l` / `L` / `Right` | 右移 |
| `k` / `K` / `Up` | 上移 |
| `j` / `J` / `Down` | 下移 |
| `g` | 移到顶部 |
| `G` | 移到底部 |
| `n` / `N` / `Enter` | 进入/确认 |
| `b` / `B` / `Esc` | 返回上一级 |
| `/` | 搜索当前列表 |
| `q` / `Q` | 退出程序 |
| `r` / `R` | 重新渲染界面 |

### 播放控制

| 按键 | 功能 |
|------|------|
| `Space` | 播放/暂停 |
| `[` | 上一首 |
| `]` | 下一首 |
| `=` | 增大音量 |
| `-` | 减小音量 |
| `p` | 切换播放模式 |
| `P` | 心动模式 |
| `x` | 快退 1 秒 |
| `X` | 快退 5 秒 |
| `v` | 快进 5 秒 |
| `V` | 快进 10 秒 |

### 列表/歌曲/歌单操作

| 按键 | 功能 |
|------|------|
| `Ctrl+u` / `PgUp` | 上一页 |
| `Ctrl+d` / `PgDown` | 下一页 |
| `c` / `C` | 显示当前播放列表 |
| `e` | 添加为下一曲播放 |
| `E` | 添加到播放列表末尾 |
| `\` | 从播放列表删除选中歌曲 |
| `,` | 喜欢播放中歌曲 |
| `.` | 取消喜欢播放中歌曲 |
| `t` | 标记播放中歌曲为不喜欢 |
| `<` | 喜欢选中歌曲 |
| `>` | 取消喜欢选中歌曲 |
| `T` | 标记选中歌曲为不喜欢 |
| `d` | 下载选中歌曲 |
| `Ctrl+l` | 下载选中歌曲歌词 |
| `` ` `` | 将播放中歌曲加入歌单 |
| `~` | 将播放歌曲从歌单中删除 |
| `Tab` | 将选中歌曲加入歌单 |
| `Shift+Tab` | 将选中歌曲从歌单中删除 |
| `;` / `:` | 收藏选中歌单 |
| `'` / `"` | 取消收藏选中歌单 |

### 浏览信息

| 按键 | 功能 |
|------|------|
| `a` / `A` | 打开播放中/选中歌曲的专辑 |
| `s` / `S` | 打开播放中/选中歌曲的歌手 |
| `o` / `O` | 网页打开播放中/选中歌曲 |
| `f` / `F` | 显示相似歌曲 |
| `?` | 显示帮助信息 |

### 实用功能

| 按键 | 功能 |
|------|------|
| `m` | 对选中项的操作菜单 |
| `M` | 对当前播放的操作菜单 |
| `u` / `U` | 清除音乐缓存 |
| `w` / `W` | 注销并退出 |
| `|` | 切换排序顺序（电台/播客列表） |

### 全局快捷键配置

默认不启用，需在配置文件中设置：

```toml
[keybindings.global]
"ctrl+shift+space" = "toggle"
```


## 配置文件

配置文件路径：

- macOS: `$HOME/Library/Application Support/go-musicfox`

可以通过设置 `MUSICFOX_ROOT` 环境变量自定义配置目录。

配置文件格式为 TOML，详细配置示例参考项目中的 `utils/filex/embed/config.toml`。

### 配置示例

```toml
[keybindings]
useDefaultKeyBindings = false

[keybindings.app]
next = "alt+n"
help = ["?","ctrl+h"]
curPlaylist = "ctrl+p"

[share]
song = "分享{{if .SongArtists}}{{.SongArtists}}的{{end}}单曲《{{.SongName}}》: {{.SongUrl}} (来自@网易云音乐)"
```

## 常见问题

### 配置文件迁移

如果旧版本配置文件仍为 INI 格式：

```bash
musicfox migrate
```

## 相关项目

- Spotifox（Spotify 版）：https://github.com/go-musicfox/spotifox
- bubbletea（UI 框架定制）：https://github.com/go-musicfox/bubbletea
- netease-music（网易云音乐 API）：https://github.com/go-musicfox/netease-music
