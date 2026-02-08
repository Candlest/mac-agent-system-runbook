---
title: OpenCode API 供应商配置指南
id: opencode-api-provider-guide
tags:
  - opencode
  - api
  - configuration
  - provider
created: 2026-02-04
updated: 2026-02-08
status: stable
summary: OpenCode API 供应商和模型的完整配置指南,包括供应商添加、模型配置、API Key 管理
---

# OpenCode API 供应商配置指南

## 快速配置步骤

### 1. 配置文件位置

- **主配置文件**: `~/.config/opencode/opencode.json` (供应商和模型配置)
- **认证文件**: `~/.local/share/opencode/auth.json` (API Key 存储)

### 2. 添加供应商

在 `~/.config/opencode/opencode.json` 中添加供应商配置：

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "供应商名称": {
      "options": {
        "baseURL": "https://api.example.com/v1"
      },
      "models": {}
    }
  }
}
```

**重要参数**:
- `供应商名称`: 自定义标识符（如 openai、anthropic、zhipuai）
- `baseURL`: API 服务的基础 URL

### 3. 配置模型

在供应商下添加模型配置：

```json
{
  "provider": {
    "供应商名称": {
      "options": {
        "baseURL": "https://api.example.com/v1"
      },
      "models": {
        "模型ID": {
          "id": "模型ID",
          "name": "模型显示名称",
          "cost": {
            "input": 3,
            "output": 15
          },
          "limit": {
            "context": 200000,
            "output": 64000
          },
          "reasoning": true,
          "temperature": true,
          "tool_call": true,
          "attachment": true
        }
      }
    }
  }
}
```

**模型参数说明**:
- `id`: 模型的唯一标识符，用于 API 调用
- `name`: 模型的显示名称
- `cost.input`: 输入 token 成本（每百万 token 美元）
- `cost.output`: 输出 token 成本（每百万 token 美元）
- `limit.context`: 最大上下文长度（token 数）
- `limit.output`: 最大输出长度（token 数）
- `reasoning`: 是否支持推理模式
- `temperature`: 是否支持温度参数调节
- `tool_call`: 是否支持函数/工具调用
- `attachment`: 是否支持文件附件

### 4. 配置 API Key

#### 方式一：使用命令行（推荐）

```bash
# 登录或更新某个供应商的 Key
opencode auth login

# 查看当前已配置的所有供应商和 Key 状态
opencode auth list

# 删除指定供应商的 Key
opencode auth logout <供应商名称>
```

#### 方式二：手动编辑配置文件

在 `~/.local/share/opencode/auth.json` 中添加：

```json
{
  "供应商名称": {
    "type": "api",
    "key": "你的API密钥"
  }
}
```

**注意**: auth.json 中的供应商名称必须与 opencode.json 中的供应商名称完全一致。

## API Key 优先级

1. **环境变量**（最高优先级）
   - 如 `ANTHROPIC_API_KEY`、`OPENAI_API_KEY`
   - 或 `OPENCODE_<供应商名称>_APIKEY`

2. **本地配置文件**（备选）
   - 使用 `~/.local/share/opencode/auth.json` 中的 Key

## 常见问题

### 问题：Invalid JSON payload received

**原因**: API 端点不支持某些参数（如工具调用中的 `ref` 字段）

**解决方案**:
1. 检查 API 文档，确认支持的参数
2. 在模型配置中禁用不支持的特性：
   ```json
   {
     "tool_call": false,
     "attachment": false
   }
   ```

### 问题：如何复用现有供应商？

某些 API 代理服务（如 yunwu.ai）支持复用 opencode 已有的提供商配置。例如：

```json
{
  "provider": {
    "anthropic": {
      "options": {
        "baseURL": "https://yunwu.ai/v1"
      }
    }
  }
}
```

这样可以复用 anthropic 的配置，只需要修改 baseURL。

### 问题：如何切换模型？

按 `Ctrl+P`，选择 "Switch Model" 即可切换已配置的模型。

## 安全建议

1. **文件权限**: 设置 auth.json 为仅当前用户可读
   ```bash
   chmod 600 ~/.local/share/opencode/auth.json
   ```

2. **版本控制**: 不要将 auth.json 提交到版本控制系统

3. **环境隔离**: 开发环境和生产环境使用不同的 API Key

4. **定期轮换**: 定期更换 API Key，使用具有适当权限限制的 Key

## 配置示例

### 完整配置示例

**opencode.json**:
```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "供应商1": {
      "options": {
        "baseURL": "https://api.供应商1.com/v1"
      },
      "models": {
        "claude-sonnet-4-5": {
          "id": "claude-sonnet-4-5",
          "name": "Claude Sonnet 4.5",
          "cost": { "input": 3, "output": 15 },
          "limit": { "context": 200000, "output": 64000 },
          "reasoning": true,
          "temperature": true,
          "tool_call": true,
          "attachment": true
        }
      }
    }
  }
}
```

**auth.json**:
```json
{
  "供应商1": {
    "type": "api",
    "key": "你的API密钥"
  }
}
```

## 参考资源

- [OpenCode 官方文档](https://opencode.ai/docs/)
- [OpenCode Providers 文档](https://opencode.ai/docs/providers/)
