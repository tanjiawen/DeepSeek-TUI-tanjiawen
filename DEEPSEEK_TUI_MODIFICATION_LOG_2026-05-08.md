# DeepSeek-TUI 搜索增强项目记录

> 日期：2026-05-08
> 操作者：家文
> 项目：DeepSeek-TUI 二次开发

---

## 一、项目背景

对 DeepSeek-TUI 项目进行搜索能力增强和本地化改造。

**源码路径：** `/Volumes/移动硬盘1T/Infrastructure/references/DeepSeek-TUI`

**远程仓库：**
- origin（自己的 fork）：`https://github.com/tanjiawen/DeepSeek-TUI-tanjiawen.git`
- upstream（原仓库）：`https://github.com/Hmbown/DeepSeek-TUI.git`（只读）

---

## 二、代码修改汇总

### 2.1 搜索增强

| 文件 | 改动类型 | 说明 |
|------|---------|------|
| `crates/tui/src/tools/tavily_search.rs` | 新增 | Tavily 搜索工具实现，422 行 |
| `crates/tui/src/tools/registry.rs` | 修改 | 条件加载逻辑：检测 `TAVILY_API_KEY` |
| `crates/tui/src/tools/mod.rs` | 修改 | 添加 `pub mod tavily_search;` |

**搜索策略：**
```
TAVILY_API_KEY 存在 → TavilySearchTool（优先）
否则 → WebSearchTool（DuckDuckGo + Bing 回退）
```

**Tavily 失败时自动回退到 DuckDuckGo。**

### 2.2 本地化修改

| 文件 | 改动 |
|------|------|
| `crates/tui/src/tools/recall_archive.rs` | `[thinking]` → `[思考内容]` |
| `crates/tui/src/tui/history.rs` | UI 文本中文化 |
| `crates/tui/src/tui/model_picker.rs` | 推理强度提示中文化 |

### 2.3 服务化部署

新增 `deploy/` 目录，包含：
- 服务化部署方案（systemd/launchd）
- Caddy 反向代理配置
- exam-bridge.py 考试系统适配层
- 预编译二进制（macOS x64/ARM64、Linux x64）

### 2.4 文档更新

`README.zh-CN.md` 新增：
- `TAVILY_API_KEY` 环境变量说明
- 搜索功能章节（Tavily + 回退机制）

---

## 三、Git 操作记录

### 3.1 仓库配置

```bash
# origin 指向自己的 fork
git remote set-url origin https://github.com/tanjiawen/DeepSeek-TUI-tanjiawen.git

# 添加上游为只读
git remote add upstream https://github.com/Hmbown/DeepSeek-TUI.git
```

### 3.2 提交记录

```
9c767d0 feat: 添加 Tavily 搜索支持及中文本地化
```

### 3.3 遇到的问题

1. **`.git/index.lock` 文件锁定**
   - 原因：macOS 文件系统与 Linux VM 交互的只读锁机制
   - 解决：在 Mac 终端执行 `rm -f .git/index.lock`

2. **Rebase 冲突（model_picker.rs）**
   - 原因：远程有初始提交，与本地修改冲突
   - 解决：`git checkout --ours crates/tui/src/tui/model_picker.rs`

3. **Vim 交换文件残留**
   - 原因：之前的 vim 编辑器异常退出
   - 解决：`rm -f .git/.COMMIT_EDITMSG.swp`

---

## 四、后续建议

1. **定期从上游更新**
   ```bash
   git fetch upstream
   git merge upstream/main
   ```

2. **开发流程**
   ```bash
   git checkout -b my-feature upstream/main
   # ... 开发 ...
   git push origin my-feature
   ```

---

## 五、相关文档

- `deploy/TECHNICAL_DESIGN.md` - 服务化技术方案
- `deploy/README.md` - 部署说明
- `crates/tui/src/tools/tavily_search.rs` - Tavily 搜索实现

---

## 六、Trae IDE 接入 DeepSeek TUI

### 6.1 DeepSeek TUI 内置 MCP Server

DeepSeek TUI 内置了 MCP 服务器功能，可以被其他 IDE（如 Trae）接入。

**启动 MCP 服务器：**
```bash
deepseek mcp-server
```

这会启动一个 stdio 模式的 MCP 服务器，供其他 IDE 连接。

### 6.2 Trae IDE 连接配置

在 Trae IDE 的 MCP 设置中，添加新的 server：

**方式 1：命令行方式**
```json
{
  "mcpServers": {
    "deepseek-tui": {
      "command": "deepseek",
      "args": ["mcp-server"]
    }
  }
}
```

**方式 2：指定路径方式**
```json
{
  "mcpServers": {
    "deepseek-tui": {
      "command": "/usr/local/bin/deepseek",
      "args": ["mcp-server"],
      "env": {
        "DEEPSEEK_API_KEY": "your-api-key"
      }
    }
  }
}
```

### 6.3 Trae MCP 配置步骤

1. 打开 Trae IDE 设置
2. 找到 **MCP Servers** 或 **Extensions** 配置项
3. 添加新的 MCP server，填入上述配置
4. 保存并重启 Trae

### 6.4 验证连接

在 Trae 中确认 MCP server 已连接成功，即可使用 DeepSeek TUI 的工具（文件搜索、web 搜索、shell 命令等）。

---

## 七、编译指南

### 7.1 环境要求

- Rust 1.88+
- 源码路径：`/Volumes/移动硬盘1T/Infrastructure/references/DeepSeek-TUI`

### 7.2 编译命令

```bash
cd /Volumes/移动硬盘1T/Infrastructure/references/DeepSeek-TUI

# 检查 Rust 环境
rustc --version
cargo --version

# 编译 release 版本
cargo build --release

# 或仅编译 tui 包
cargo build --release -p deepseek-tui

# 验证编译结果
./target/release/deepseek --version
```

### 7.3 安装 Rust（如未安装）

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source ~/.cargo/env
```