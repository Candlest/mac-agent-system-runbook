---
title: Zsh Fish-like 补全/高亮
id: zsh-fishlike
tags:
  - zsh
  - completion
  - highlight
  - mise
  - homebrew
created: 2026-02-04
updated: 2026-02-04
status: stable
summary: 用 zsh-autocomplete + fast-syntax-highlighting 做出接近 fish 的补全/高亮，并补齐 brew/mise 工具的 option 补全。
---

# Zsh: Fish-like 补全/高亮（轻量方案）

目标：在 zsh + starship 下获得接近 fish 的「灰字建议 + 实时补全菜单 + 语法高亮」，不使用 oh-my-zsh。

## 组件

- `marlonrichert/zsh-autocomplete`
  - 提供：实时补全菜单、type-ahead/灰字建议、历史/目录补全等
  - 备注：它会管理 `compinit`，所以不要再手动 `compinit`

- `zdharma-continuum/fast-syntax-highlighting`
  - 提供：输入时语法高亮、错误提示

- `starship`
  - 提供：轻量、可定制的提示符
  - 备注：若使用图标符号，终端字体需支持 Nerd Font

## 安装位置（本机约定）

- 插件目录：`~/.zsh/plugins/`
  - `~/.zsh/plugins/zsh-autocomplete/`
  - `~/.zsh/plugins/fsh/`
- 补全缓存目录：`~/.zsh/cache/`
- Starship 配置：`~/.config/starship.toml`

## Starship（本机配置节选）

配置文件：`~/.config/starship.toml`

```toml
format = """
$username\
$hostname\
$directory\
$git_branch\
$git_status\
$fill\
$cmd_duration\
$time\
$line_break\
$character"""

[git_status]
format = '([ • $all_status$ahead_behind]($style))[](fg:76) '

[time]
disabled = false
```

## 启用 Starship

1. 安装

```sh
brew install starship
```

2. 在 `~/.zshrc` 里启用（推荐按需加载）

```sh
if command -v starship >/dev/null 2>&1; then
  eval "$(starship init zsh)"
fi
```

3. 重载 shell

```sh
exec zsh
```

## 更新

本机采用「直接 git clone」安装，更新方式如下：

```sh
git -C ~/.zsh/plugins/zsh-autocomplete pull --ff-only
git -C ~/.zsh/plugins/fsh pull --ff-only
```

更新后重载 shell：

```sh
exec zsh
```

## 为 mise 安装的软件补全 option

mise 只负责把工具放进 PATH。要让 zsh 能补全这些工具的 `--option`，需要把补全函数（`_cmd` 文件）放进 `fpath`。

本机做法：

- 自维护补全目录：`~/.zsh/completions/`
- `~/.zshrc` 会把该目录加入 `fpath`（且发生在 `compinit` 之前）

生成/更新补全文件（按需执行）：

```sh
mkdir -p ~/.zsh/completions

mise exec -- uv  generate-shell-completion zsh > ~/.zsh/completions/_uv
mise exec -- pnpm completion zsh               > ~/.zsh/completions/_pnpm
mise exec -- bun  completions zsh              > ~/.zsh/completions/_bun
opencode completion zsh                       > ~/.zsh/completions/_opencode

rm -f ~/.zcompdump* && exec zsh
```

## 非 mise 安装的工具

有些工具（如 opencode）不通过 mise 安装，但同样提供 shell 补全命令，可直接生成：

```sh
opencode completion zsh > ~/.zsh/completions/_opencode
```

## 维护要点

- `zsh-autocomplete` 需要在 `~/.zshrc` 很靠前的位置 `source`（在任何 `compdef` 之前）。
- `fast-syntax-highlighting` 可能对某些第三方 ZLE widget 打印告警；为保持启动安静，可对它的 `source` 做 `2>/dev/null`。
- `mise` / `micromamba` / `starship` 都建议按需加载（存在才 eval），减少启动报错并略微提升性能。
