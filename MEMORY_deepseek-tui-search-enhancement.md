---
name: deepseek-tui-search-enhancement
description: 2026-05-08 DeepSeek-TUI 搜索增强项目，添加 Tavily 搜索支持及中文本地化
type: project
---

# DeepSeek-TUI 搜索增强项目

**日期：** 2026-05-08
**操作者：** 家文

## 背景
对 DeepSeek-TUI 项目进行搜索能力增强和本地化改造。

## 主要改动

### 搜索增强
- 新增 `tavily_search.rs`：Tavily API 搜索工具，带 DuckDuckGo 回退
- 修改 `registry.rs`：条件加载 Tavily（检测 `TAVILY_API_KEY` 环境变量）
- 修改 `mod.rs`：添加 tavily_search 模块

### 本地化
- `recall_archive.rs`、`history.rs`、`model_picker.rs`：UI 文本中文化

### 服务化
- 新增 `deploy/` 目录：systemd/launchd 服务、Caddy 反向代理、exam-bridge.py

### 文档
- `README.zh-CN.md`：新增 TAVILY_API_KEY 说明和搜索功能章节

## Git 仓库
- **origin（可写）：** `https://github.com/tanjiawen/DeepSeek-TUI-tanjiawen.git`
- **upstream（只读）：** `https://github.com/Hmbown/DeepSeek-TUI.git`

## 遇到的问题
1. `.git/index.lock` 锁定 → Mac 终端 `rm -f .git/index.lock`
2. rebase 冲突 → `git checkout --ours` 保留本地版本
3. vim 交换文件残留 → `rm -f .git/.COMMIT_EDITMSG.swp`

## 相关文档
- `DEEPSEEK_TUI_MODIFICATION_LOG_2026-05-08.md` - 详细修改记录
- `deploy/TECHNICAL_DESIGN.md` - 服务化技术方案
- `README.zh-CN.md` - 新增 TAVILY_API_KEY 说明和搜索功能章节

## Trae IDE 接入
- DeepSeek TUI 内置 MCP Server：`deepseek mcp-server`
- Trae 可通过 MCP 配置接入 DeepSeek TUI
- 参考 `DEEPSEEK_TUI_MODIFICATION_LOG_2026-05-08.md` 第六章