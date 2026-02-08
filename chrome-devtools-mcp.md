---
title: chrome-devtools-mcp 配置与维护
id: chrome-devtools-mcp
tags:
  - mcp
  - opencode
  - chrome
  - devtools
  - automation
  - browser
created: 2026-02-08
updated: 2026-02-08
status: stable
summary: Chrome DevTools MCP 服务器在 OpenCode 中的安装、配置与日常维护指南
source: https://github.com/ChromeDevTools/chrome-devtools-mcp
---

# chrome-devtools-mcp 配置与维护

## 背景

`chrome-devtools-mcp` 是一个 Model Context Protocol (MCP) 服务器,让 AI 编程助手(如 OpenCode、Claude、Cursor)能够控制和检查实时运行的 Chrome 浏览器。它通过 Chrome DevTools 协议提供可靠的自动化、深度调试和性能分析能力。

### 主要功能

- **性能洞察**: 使用 Chrome DevTools 记录性能跟踪并提取可操作的性能见解
- **高级浏览器调试**: 分析网络请求、截图、检查浏览器控制台消息(带源映射堆栈跟踪)
- **可靠的自动化**: 使用 Puppeteer 自动化 Chrome 操作并自动等待操作结果

### 工具清单 (共 26 个)

- **输入自动化** (8 个): `click`, `drag`, `fill`, `fill_form`, `handle_dialog`, `hover`, `press_key`, `upload_file`
- **导航自动化** (6 个): `navigate_page`, `new_page`, `close_page`, `list_pages`, `select_page`, `wait_for`
- **性能分析** (3 个): `performance_start_trace`, `performance_stop_trace`, `performance_analyze_insight`
- **网络调试** (2 个): `list_network_requests`, `get_network_request`
- **调试工具** (5 个): `take_screenshot`, `take_snapshot`, `evaluate_script`, `list_console_messages`, `get_console_message`
- **设备模拟** (2 个): `emulate`, `resize_page`

## 前置要求

- Node.js v20.19 或更新的 LTS 版本
- Chrome 当前稳定版或更新版本
- npm/npx 工具

## 安装步骤

### 1. 配置文件位置

OpenCode 配置文件: `~/.config/opencode/opencode.json`

### 2. 标准配置(推荐)

在 `opencode.json` 的 `mcp` 对象中添加以下配置:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "chrome-devtools": {
      "type": "local",
      "command": [
        "npx",
        "-y",
        "chrome-devtools-mcp@latest",
        "--isolated=true",
        "--headless=false",
        "--no-usage-statistics"
      ],
      "environment": {
        "PATH": "/Users/candlest/.local/share/mise/shims:/usr/local/bin:/usr/bin:/bin"
      },
      "enabled": true
    }
  }
}
```

### 3. 配置参数说明

| 参数 | 说明 | 推荐值 |
|------|------|--------|
| `--isolated=true` | 使用临时用户数据目录,关闭后自动清理 | `true` (保持环境干净) |
| `--headless=false` | 显示浏览器界面 | `false` (便于调试) |
| `--no-usage-statistics` | 禁用使用统计收集 | 启用 (隐私保护) |
| `@latest` | 始终使用最新版本 | 推荐 (自动获取更新) |

### 4. 验证安装

重启 OpenCode 后,使用以下提示词测试:

```
检查 https://developers.chrome.com 的性能
```

或

```
打开 https://github.com 并截图
```

## 常见使用场景

### 1. 性能分析

```
检查 https://example.com 的性能,分析首屏加载时间和核心 Web 指标
```

### 2. 页面截图

```
访问 https://example.com 并对整个页面截图
```

### 3. 网络请求调试

```
打开 https://api.example.com 并列出所有失败的网络请求
```

### 4. 表单自动填充

```
访问 https://example.com/login,填写用户名为 test@example.com,密码为 TestPass123
```

### 5. 控制台日志分析

```
访问 https://example.com,列出所有控制台错误和警告
```

## 配置模式对比

### 模式一: 自动启动(推荐)

**适用场景**: 大多数日常使用场景

```json
{
  "command": [
    "npx", "-y", "chrome-devtools-mcp@latest",
    "--isolated=true",
    "--headless=false"
  ]
}
```

**特点**:
- ✅ 无需手动启动浏览器
- ✅ 配置简单
- ✅ 支持临时隔离环境

### 模式二: 连接运行中的实例

**适用场景**: 需要保持登录状态、在沙箱环境中运行

**步骤**:

1. **启动 Chrome (带远程调试)**:
```bash
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --remote-debugging-port=9222 \
  --user-data-dir=/tmp/chrome-profile-stable
```

2. **修改配置**:
```json
{
  "command": [
    "npx", "-y", "chrome-devtools-mcp@latest",
    "--browser-url=http://127.0.0.1:9222"
  ]
}
```

**特点**:
- ✅ 保持浏览器会话状态
- ✅ 适合沙箱环境
- ⚠️ 需要手动启动浏览器
- ⚠️ 有安全风险(远程调试端口暴露)

### 模式三: 自动连接(Chrome 144+)

**适用场景**: 在手动测试和代理测试之间切换

```json
{
  "command": [
    "npx", "-y", "chrome-devtools-mcp@latest",
    "--autoConnect"
  ]
}
```

**特点**:
- ✅ 自动发现运行中的 Chrome
- ✅ 无需配置端口
- ⚠️ 需要 Chrome 144+ 版本
- ⚠️ 需要在 `chrome://inspect/#remote-debugging` 中启用远程调试

## 高级配置选项

### 完整配置示例

```json
{
  "command": [
    "npx", "-y", "chrome-devtools-mcp@latest",
    "--isolated=true",
    "--headless=false",
    "--no-usage-statistics",
    "--viewport=1920x1080",
    "--channel=stable",
    "--accept-insecure-certs=false",
    "--chrome-arg=--disable-gpu",
    "--chrome-arg=--no-sandbox"
  ],
  "environment": {
    "PATH": "/Users/candlest/.local/share/mise/shims:/usr/local/bin:/usr/bin:/bin",
    "CHROME_DEVTOOLS_MCP_NO_USAGE_STATISTICS": "1"
  },
  "enabled": true
}
```

### 常用参数表

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--headless` | boolean | false | 是否无头模式运行 |
| `--isolated` | boolean | false | 使用临时用户数据目录 |
| `--viewport` | string | - | 初始视口大小,如 `1920x1080` |
| `--channel` | string | stable | Chrome 渠道: stable/canary/beta/dev |
| `--user-data-dir` | string | auto | 自定义用户数据目录 |
| `--executable-path` | string | auto | 自定义 Chrome 可执行文件路径 |
| `--proxy-server` | string | - | 代理服务器配置 |
| `--accept-insecure-certs` | boolean | false | 忽略证书错误(谨慎使用) |
| `--chrome-arg` | array | - | 传递给 Chrome 的额外参数 |
| `--performance-crux` | boolean | true | 是否启用 CrUX API 获取字段性能数据 |
| `--usage-statistics` | boolean | true | 是否收集使用统计 |

### 环境变量

| 变量名 | 说明 |
|--------|------|
| `CHROME_DEVTOOLS_MCP_NO_USAGE_STATISTICS` | 设置为 `1` 禁用统计收集 |
| `DEBUG` | 设置为 `*` 启用详细日志 |

## 故障排查

### 问题 1: 无法启动 Chrome

**症状**: MCP 服务器报错 "Failed to launch Chrome"

**解决方案**:

1. 检查 Chrome 是否已安装:
```bash
ls /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome
```

2. 检查 PATH 环境变量是否正确配置

3. 尝试手动指定 Chrome 路径:
```json
{
  "command": [
    "npx", "-y", "chrome-devtools-mcp@latest",
    "--executable-path=/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
  ]
}
```

### 问题 2: 连接超时

**症状**: "Connection timeout" 或 "Failed to connect"

**解决方案**:

1. 检查防火墙设置
2. 确认远程调试端口未被占用:
```bash
lsof -i :9222
```

3. 增加启动超时时间(在 OpenCode 配置中):
```json
{
  "mcp": {
    "chrome-devtools": {
      "timeout": 30000
    }
  }
}
```

### 问题 3: 沙箱限制

**症状**: 在某些 MCP 客户端中无法启动 Chrome

**解决方案**: 使用 `--browser-url` 模式,手动启动 Chrome

### 问题 4: 端口冲突

**症状**: "Address already in use"

**解决方案**:

1. 查找占用端口的进程:
```bash
lsof -i :9222
```

2. 终止占用进程或使用不同端口:
```json
{
  "command": [
    "npx", "-y", "chrome-devtools-mcp@latest",
    "--browser-url=http://127.0.0.1:9223"
  ]
}
```

## 日常维护

### 1. 更新到最新版本

配置中使用 `@latest` 标签会自动使用最新版本,无需手动更新。

手动清理 npm 缓存:
```bash
npm cache clean --force
npx clear-npx-cache
```

### 2. 清理临时文件

如果使用持久化用户数据目录:
```bash
rm -rf ~/.cache/chrome-devtools-mcp/
```

### 3. 查看日志

启用详细日志:
```json
{
  "command": [
    "npx", "-y", "chrome-devtools-mcp@latest",
    "--log-file=/tmp/chrome-devtools-mcp.log"
  ],
  "environment": {
    "DEBUG": "*"
  }
}
```

查看日志:
```bash
tail -f /tmp/chrome-devtools-mcp.log
```

### 4. 性能优化

**切换到无头模式**(节省资源):
```json
{
  "command": [
    "npx", "-y", "chrome-devtools-mcp@latest",
    "--isolated=true",
    "--headless=true"
  ]
}
```

**禁用不需要的功能**:
```json
{
  "command": [
    "npx", "-y", "chrome-devtools-mcp@latest",
    "--category-performance=false",
    "--performance-crux=false"
  ]
}
```

## 安全注意事项

1. **远程调试端口**: 启用 `--browser-url` 模式时,确保端口仅在本地监听,不要暴露到公网

2. **敏感数据**: MCP 客户端可以访问浏览器中的所有数据,使用时避免访问包含敏感信息的网站

3. **证书验证**: 除非绝对必要,不要使用 `--accept-insecure-certs`

4. **用户数据目录**: 使用 `--isolated=true` 避免污染主浏览器配置

5. **代理配置**: 使用代理时注意凭证保护

## 与其他工具集成

### 1. 与 Playwright 共存

如果同时使用 Playwright,建议使用不同的用户数据目录:

```json
{
  "command": [
    "npx", "-y", "chrome-devtools-mcp@latest",
    "--user-data-dir=/tmp/chrome-mcp-profile"
  ]
}
```

### 2. 与 Selenium 共存

确保端口不冲突,Selenium 默认使用 4444 端口,chrome-devtools-mcp 使用动态端口或 9222。

### 3. CI/CD 环境

在 CI 环境中使用无头模式和临时目录:

```json
{
  "command": [
    "npx", "-y", "chrome-devtools-mcp@latest",
    "--isolated=true",
    "--headless=true",
    "--no-usage-statistics"
  ],
  "environment": {
    "CI": "true"
  }
}
```

## 参考资源

- [官方 GitHub 仓库](https://github.com/ChromeDevTools/chrome-devtools-mcp)
- [工具参考文档](https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/docs/tool-reference.md)
- [故障排查指南](https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/docs/troubleshooting.md)
- [设计原则](https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/docs/design-principles.md)
- [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/)
- [Puppeteer 文档](https://pptr.dev/)

## 版本历史

- 2026-02-08: 初始版本,基于 chrome-devtools-mcp v0.16.0+

## 相关文档

- [opencode-skill-global.md](./opencode-skill-global.md) - OpenCode 全局 skill 配置
- [AGENT.md](./AGENT.md) - OpenCode agent 配置与指令
