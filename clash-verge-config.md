---
title: Clash Verge 配置指南
id: clash-verge-config
tags:
  - clash
  - proxy
  - network
  - vpn
created: 2026-02-13
updated: 2026-02-13
status: stable
summary: Clash Verge 代理配置文件管理、专用节点组创建、域名规则配置。
---

# Clash Verge 配置指南

## 配置文件位置

```
~/Library/Application Support/io.github.clash-verge-rev.clash-verge-rev/profiles/Rdxeh50IWD2n.yaml
```

## 创建专用节点组

### 添加 GPT 日本节点组

在 `proxy-groups` 部分添加新组（在 `故障转移` 组之后）：

```yaml
- name: GPT-日本
  type: select
  proxies:
    - Silver-日本-LMT-03
    - Silver-日本-商宽-01
    - Silver-日本-商宽-02
    - Bronze-日本-01
```

**优先级说明：**
1. LMT 节点优先
2. Silver 商宽节点次之
3. Bronze 节点最后

## 配置域名规则

### 修改现有规则

将 `openai.com` 从 `TapFog` 组改为 `GPT-日本` 组：

```yaml
- DOMAIN-SUFFIX,openai.com,GPT-日本
```

### 添加 GPT 相关规则

在 `openai.com` 规则之后添加：

```yaml
- DOMAIN,chat.openai.com,GPT-日本
- DOMAIN,api.openai.com,GPT-日本
- DOMAIN,cdn.openai.com,GPT-日本
- DOMAIN,auth.openai.com,GPT-日本
- DOMAIN,platform.openai.com,GPT-日本
- DOMAIN-KEYWORD,chatgpt,GPT-日本
```

## 生效方式

修改配置文件后，需要在 Clash Verge 中重启核心或重新加载配置。

## 节点列表

### 日本节点
- Bronze-日本-01
- Silver-日本-商宽-01
- Silver-日本-商宽-02
- Silver-日本-LMT-03

### 代理组
- TapFog：默认全局代理组
- 自动选择：自动选择最快节点
- 故障转移：故障转移模式
- GPT-日本：GPT 专用日本节点组

## 常见域名规则

### DIRECT（直连）
- 国内常见域名（baidu.com, taobao.com, qq.com 等）
- Apple 相关域名
- 国内 CDN 域名

### TapFog（全局代理）
- Google 相关
- YouTube
- Facebook
- Twitter
- Instagram
- GitHub
- 大部分国外网站

### GPT-日本（日本节点）
- openai.com
- chat.openai.com
- api.openai.com
- cdn.openai.com
- auth.openai.com
- platform.openai.com
- 包含 chatgpt 关键字的域名

### REJECT（拒绝）
- 广告相关域名
- 追踪域名
