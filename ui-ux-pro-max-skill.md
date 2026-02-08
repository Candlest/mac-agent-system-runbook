---
title: UI UX Pro Max Skill 安装与配置
id: ui-ux-pro-max-skill
tags:
  - opencode
  - skill
  - ui-ux
  - python
  - uv
created: 2026-02-08
updated: 2026-02-08
status: stable
summary: 安装 UI UX Pro Max design system skill 到 OpenCode 全局,使用 uv 虚拟环境管理 Python 依赖
---

# UI UX Pro Max Skill 安装与配置

## 目标

安装 UI UX Pro Max design system skill 到 OpenCode 全局环境,提供专业 UI/UX 设计指导,包括 67 种样式、96 种配色方案、57 种字体组合、99 条 UX 准则和 25 种图表类型。

## 前置条件

- [x] pnpm/npm (包管理器)
- [x] Python 3.x (推荐通过 uv 管理虚拟环境)
- [x] OpenCode 已安装
- [x] uv (Python 虚拟环境管理器)

## 安装步骤

### 1. 全局安装 uipro-cli

```bash
pnpm install -g uipro-cli
```

这会安装 `uipro` 命令行工具,版本 2.2.3+。

### 2. 初始化 Skill 到 OpenCode

```bash
cd ~/.config/opencode
uipro init --ai opencode
```

执行后会生成以下目录结构:

```
~/.config/opencode/.opencode/skills/ui-ux-pro-max/
├── SKILL.md
├── data/
│   ├── styles.csv
│   ├── colors.csv
│   ├── charts.csv
│   ├── landing.csv
│   ├── typography.csv
│   ├── prompts.csv
│   ├── ux.csv
│   ├── products.csv
│   ├── ui-reasoning.csv
│   └── stacks.csv
└── scripts/
    ├── search.py
    ├── core.py
    └── design_system.py
```

### 3. 创建 Python 虚拟环境

```bash
cd ~/.config/opencode/.opencode/skills/ui-ux-pro-max
uv venv .venv
```

这会创建独立的 Python 3.13.5 虚拟环境,避免污染系统 Python。

### 4. 安装 Python 依赖

**注意:** UI UX Pro Max 的脚本只使用 Python 标准库,无需安装额外的第三方依赖。

### 5. 修改脚本使用虚拟环境

修改三个 Python 脚本的 shebang 行,指向虚拟环境的 Python:

```bash
# 查看当前 shebang
head -1 scripts/*.py

# 修改为绝对路径
sed -i '' '1s|.*|#!/Users/candlest/.config/opencode/.opencode/skills/ui-ux-pro-max/.venv/bin/python|' scripts/search.py
sed -i '' '1s|.*|#!/Users/candlest/.config/opencode/.opencode/skills/ui-ux-pro-max/.venv/bin/python|' scripts/core.py
sed -i '' '1s|.*|#!/Users/candlest/.config/opencode/.opencode/skills/ui-ux-pro-max/.venv/bin/python|' scripts/design_system.py
```

### 6. 添加执行权限

```bash
chmod +x scripts/*.py
```

### 7. 修复 Windows 行尾符(如需要)

如果脚本无法执行,可能是 Windows 的 CRLF 行尾符问题:

```bash
cd scripts
sed -i '' 's/\r$//' search.py core.py design_system.py
```

### 8. 测试 Skill 功能

**测试搜索功能:**

```bash
cd ~/.config/opencode/.opencode/skills/ui-ux-pro-max
./scripts/search.py "glassmorphism" --domain style
```

**测试设计系统生成:**

```bash
./scripts/search.py "SaaS dashboard" --design-system -p "TestApp"
```

如果命令正常输出结果,说明安装成功。

### 9. 重启 OpenCode

让新安装的 skill 生效。

## 使用方法

### 基本搜索

```bash
~/.config/opencode/.opencode/skills/ui-ux-pro-max/scripts/search.py "<keyword>" --domain <domain>
```

**可用域名:**
- `style` - UI 样式
- `color` - 配色方案
- `typography` - 字体组合
- `chart` - 图表类型
- `ux` - UX 最佳实践
- `landing` - 落地页结构
- `product` - 产品类型推荐

### 生成设计系统

```bash
~/.config/opencode/.opencode/skills/ui-ux-pro-max/scripts/search.py "<product_type> <keywords>" --design-system -p "Project Name"
```

**示例:**

```bash
# 美容 SPA 设计系统
~/.config/opencode/.opencode/skills/ui-ux-pro-max/scripts/search.py "beauty spa wellness service elegant" --design-system -p "Serenity Spa"

# SaaS 仪表板设计系统
~/.config/opencode/.opencode/skills/ui-ux-pro-max/scripts/search.py "SaaS dashboard analytics" --design-system -p "MyAnalytics"
```

### 持久化设计系统(可选)

```bash
~/.config/opencode/.opencode/skills/ui-ux-pro-max/scripts/search.py "<query>" --design-system --persist -p "Project Name"
```

这会创建 `design-system/MASTER.md` 和 `design-system/pages/*.md` 文件,用于跨会话保存设计系统规则。

### 技术栈特定指南

```bash
~/.config/opencode/.opencode/skills/ui-ux-pro-max/scripts/search.py "<keyword>" --stack html-tailwind
```

**可用技术栈:**
- `html-tailwind` (默认)
- `react`
- `nextjs`
- `vue`
- `svelte`
- `swiftui`
- `react-native`
- `flutter`
- `shadcn`
- `jetpack-compose`

## 维护与更新

### 更新 Skill

```bash
# 更新 uipro-cli
pnpm update -g uipro-cli

# 重新初始化 skill
rm -rf ~/.config/opencode/.opencode/skills/ui-ux-pro-max
cd ~/.config/opencode
uipro init --ai opencode
```

### 重新配置虚拟环境

```bash
cd ~/.config/opencode/.opencode/skills/ui-ux-pro-max

# 删除旧虚拟环境
rm -rf .venv

# 创建新虚拟环境
uv venv .venv

# 修改脚本 shebang (参考安装步骤 5)
```

## 故障排除

### 问题 1: 权限拒绝

```bash
chmod +x scripts/*.py
```

### 问题 2: Bad interpreter

```bash
# 修复 Windows 行尾符
cd scripts
sed -i '' 's/\r$//' search.py core.py design_system.py
```

### 问题 3: 找不到 Python

确认虚拟环境路径正确:

```bash
ls -la .venv/bin/python
```

### 问题 4: OpenCode 无法识别 skill

1. 确认目录路径正确: `~/.config/opencode/.opencode/skills/ui-ux-pro-max/`
2. 检查 `SKILL.md` 的 frontmatter 格式
3. 重启 OpenCode

## 参考资源

- 官方文档: https://uupm.cc
- GitHub: https://github.com/nextlevelbuilder/ui-ux-pro-max-skill
- NPM: https://www.npmjs.com/package/uipro-cli

## 变更日志

- **2026-02-08**: 初始安装,配置 Python 虚拟环境
