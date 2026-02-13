---
title: OpenCode MCP 服务器默认禁用与按需开启
id: opencode-mcp-config
tags:
  - opencode
  - mcp
  - configuration
created: 2026-02-09
updated: 2026-02-09
status: stable
summary: OpenCode MCP 服务器默认禁用、按需开启的配置方法与最佳实践。
---

# 背景

MCP（Model Context Protocol）服务器会显著增加 LLM 上下文量，容易超出 context limit。默认禁用不需要的 MCP 服务器可以：

- 减少不必要的上下文开销
- 避免启动时加载未使用的工具
- 提升整体响应速度

# 做法

## 1. 全局禁用全部 MCP

编辑 `~/.config/opencode/opencode.json`，在 `tools` 配置中添加禁用规则：

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": ["/Users/candlest/.config/opencode/instructions/zh.md"],
  "mcp": {
    "drawio": {
      "type": "local",
      "command": ["/path/to/drawio-mcp"],
      "enabled": true
    },
    "MiniMax": {
      "type": "local",
      "command": ["uvx", "minimax-coding-plan-mcp", "-y"],
      "enabled": true
    },
    "paper-search-mcp": {
      "type": "local",
      "command": ["/path/to/paper-search-mcp/.venv/bin/python", "-m", "paper_search_mcp.server"],
      "enabled": true
    },
    "chrome-devtools": {
      "type": "local",
      "command": ["npx", "-y", "chrome-devtools-mcp@latest"],
      "enabled": true
    }
  },
  "tools": {
    "drawio_*": false,
    "MiniMax_*": false,
    "paper-search-mcp_*": false,
    "chrome-devtools_*": false
  },
  "plugin": []
}
```

## 2. 按需开启 MCP 服务器

### 2.1 在 AGENTS.md 中声明使用场景

在项目根目录的 `AGENTS.md` 中添加：

```markdown
## Chrome DevTools

当需要进行浏览器自动化、网页测试、表单填写、截图等任务时，使用 `chrome-devtools` 工具。

## Draw.io

当需要创建或编辑图表时，使用 `drawio_*` 工具。

## 论文搜索

当需要搜索学术论文时，使用 `paper-search-mcp_*` 工具。
```

### 2.2 为特定 Agent 开启

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "chrome-devtools": {
      "type": "local",
      "command": ["npx", "-y", "chrome-devtools-mcp@latest"],
      "enabled": true
    }
  },
  "tools": {
    "chrome-devtools_*": false
  },
  "agent": {
    "browser-agent": {
      "tools": {
        "chrome-devtools_*": true
      }
    }
  }
}
```

### 2.3 临时开启（对话级别）

在对话中直接要求使用 MCP 服务器，OpenCode 会动态加载：

```
使用 chrome-devtools 工具帮我填写表单并提交
```

## 3. 验证配置

运行以下命令验证 MCP 状态：

```bash
opencode mcp list
```

输出示例：

```
drawio: disabled (tools: 0)
MiniMax: disabled (tools: 0)
paper-search-mcp: disabled (tools: 0)
chrome-devtools: disabled (tools: 0)
```

## 4. 回滚/排错

### 4.1 重新启用某个 MCP 服务器

删除对应的 `tools` 禁用规则，或设为 `true`：

```json
{
  "tools": {
    "chrome-devtools_*": true  // 改为 true
  }
}
```

### 4.2 排查 MCP 加载问题

```bash
# 查看 MCP 列表和状态
opencode mcp list

# 调试特定 MCP 服务器
opencode mcp debug <server-name>

# 查看 MCP 认证状态（远程服务器）
opencode mcp auth list
```

### 4.3 常见问题

**Q: MCP 服务器已启用但工具不可用？**
A: 检查 `tools` 配置是否覆盖了 `mcp` 的 `enabled` 设置。glob 模式 `servername_*` 会禁用该服务器的所有工具。

**Q: 如何只禁用部分 MCP 工具？**
A: 使用更精确的工具名，而非 `servername_*` 通配符。

```json
{
  "tools": {
    "chrome-devtools_take_screenshot": false  // 仅禁用截图工具
  }
}
```
