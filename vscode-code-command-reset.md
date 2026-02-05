---
title: VS Code code 命令重启失效修复
id: vscode-code-command-reset
tags:
  - macos
  - vscode
  - shell
  - path
created: 2026-02-05
updated: 2026-02-05
status: stable
summary: 解决 macOS 上 VS Code `code` 命令重启后失效的问题。
---

# VS Code code 命令重启失效修复

## 背景

macOS 上 `code` 命令在当前终端可用，但重启后失效。

## 常见原因

- VS Code 放在 `~/Downloads` 或被移动，导致旧软链指向 App Translocation 临时路径
- PATH 没有持久化加载 `code` 的 bin 目录

## 解决办法

1) 把 VS Code 放到 `/Applications`

```sh
mv "$HOME/Downloads/Visual Studio Code.app" "/Applications/Visual Studio Code.app"
```

2) 持久化 PATH（zsh）

```sh
echo 'export PATH="/Applications/Visual Studio Code.app/Contents/Resources/app/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

3) 如果存在旧软链且指向 App Translocation，清理它

```sh
ls -l /usr/local/bin/code
sudo rm -f /usr/local/bin/code
```

4) （可选）在 VS Code 中安装 shell 命令

- Command Palette: `Shell Command: Install 'code' command in PATH`

## 验证

```sh
which code
code --version
```

## 回滚/排错

- 移除 PATH 追加：编辑 `~/.zshrc` 删除对应行后 `source ~/.zshrc`
- 如果 `which code` 仍为空：确认 VS Code 位置在 `/Applications`，并重新执行步骤 2
- 若使用非 zsh（如 bash）：将 PATH 写入 `~/.bash_profile` 或 `~/.profile`
