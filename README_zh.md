# Tavily MCP Server

![GitHub Repo stars](https://img.shields.io/github/stars/tavily-ai/tavily-mcp?style=social)
![npm](https://img.shields.io/npm/dt/tavily-mcp)
![smithery badge](https://smithery.ai/badge/@tavily-ai/tavily-mcp)

[English](README.md) | 简体中文

Tavily MCP Server 提供以下功能：
- search, extract, map, crawl 工具
- 通过 tavily-search 工具提供实时网页搜索能力
- 通过 tavily-extract 工具提供智能网页内容提取
- 强大的网站结构化地图生成工具
- 系统化网站爬取工具

### 📚 相关资源
- [教程](https://medium.com/@dustin_36183/building-a-knowledge-graph-assistant-combining-tavily-and-neo4j-mcp-servers-with-claude-db92de075df9)：结合 Tavily MCP 与 Neo4j MCP Server 构建知识图谱助手
- [教程](https://medium.com/@dustin_36183/connect-your-coding-assistant-to-the-web-integrating-tavily-mcp-with-cline-in-vs-code-5f923a4983d1)：在 VS Code 中将 Tavily MCP 集成到 Cline

## 远程 MCP 服务器

直接连接 Tavily 的远程 MCP 服务器，无需本地安装和配置，获得无缝体验。

只需使用你的 Tavily API Key 访问远程 MCP 服务器 URL：

``` 
https://mcp.tavily.com/mcp/?tavilyApiKey=<your-api-key> 
```
API Key 请从 [tavily.com](https://www.tavily.com/) 获取。

如果你的 MCP 客户端支持，也可以通过 `Authorization` 头传递 API Key：

```
Authorization: Bearer <your-api-key>
```

**注意**：使用远程 MCP 时，可以通过 `DEFAULT_PARAMETERS` 头指定一个 JSON 对象来设置所有请求的默认参数。示例：

```json
{"include_images":true, "search_depth": "basic", "max_results": 10}
```

## 连接到 Claude Code

[Claude Code](https://docs.anthropic.com/en/docs/claude-code) 是 Anthropic 官方的 Claude CLI 工具。你可以使用 `claude mcp add` 命令添加 Tavily MCP 服务器。有两种认证方式：

#### 方式 1：在 URL 中传递 API Key

直接在 URL 中传入你的 API Key。将 `<your-api-key>` 替换为你的 [Tavily API Key](https://www.tavily.com/)：

```bash
claude mcp add --transport http tavily https://mcp.tavily.com/mcp/?tavilyApiKey=<your-api-key>
```

#### 方式 2：OAuth 认证流程

在 URL 中不传入 API Key 添加服务器：

```bash
claude mcp add --transport http tavily https://mcp.tavily.com/mcp
```

添加完成后，需要完成认证流程：
1. 运行 `claude` 启动 Claude Code
2. 输入 `/mcp` 打开 MCP 服务器管理
3. 选择 Tavily 服务器并完成认证过程

**提示**：在命令中添加 `--scope user` 可使 Tavily MCP 服务器在所有项目中全局可用：

```bash
claude mcp add --transport http --scope user tavily https://mcp.tavily.com/mcp/?tavilyApiKey=<your-api-key>
```

配置完成后，即可使用 Tavily 的 search、extract、map 和 crawl 工具。

## 连接到 Cursor
[![Install MCP Server](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/en/install-mcp?name=tavily-remote-mcp&config=eyJjb21tYW5kIjoibnB4IC15IG1jcC1yZW1vdGUgaHR0cHM6Ly9tY3AudGF2aWx5LmNvbS9tY3AvP3RhdmlseUFwaUtleT08eW91ci1hcGkta2V5PiIsImVudiI6e319)

点击 ⬆️ Add to Cursor ⬆️ 按钮，大部分操作会自动完成，但仍需编辑配置以添加你的 API Key。你可以在 [这里](https://www.tavily.com/) 获取 Tavily API Key。

点击按钮后，你会被重定向到 Cursor ...

### 步骤 1
点击安装按钮

![](assets/cursor-step1.png)

### 步骤 2
你应该看到 MCP 已安装，如果蓝色开关未开启，请手动打开。同时需要编辑配置以填入你的 Tavily API Key。
![](assets/cursor-step2.png)

### 步骤 3
随后你会被重定向到 `mcp.json` 文件，在其中添加你的 API Key。

```json
{
  "mcpServers": {
    "tavily-remote-mcp": {
      "command": "npx -y mcp-remote https://mcp.tavily.com/mcp/?tavilyApiKey=<your-api-key>",
      "env": {}
    }
  }
}
```

### 远程 MCP 服务器 OAuth 流程

Tavily 远程 MCP 服务器支持安全的 OAuth 认证，允许你与兼容的客户端无缝连接和授权。

#### 如何设置 OAuth 认证

**A. 使用 MCP Inspector：**

* 打开 MCP Inspector 并点击 "Open Auth Settings"。
* 选择 OAuth 流程并完成以下步骤：
   1. 元数据发现
   2. 客户端注册
   3. 准备授权
   4. 请求授权并获取授权码
   5. Token 请求
   6. 认证完成

完成后，你将获得一个访问令牌，可用于安全地向 Tavily 远程 MCP 服务器发起认证请求。

**B. 使用其他 MCP 客户端（示例：Cursor）：**

你可以配置 MCP 客户端使用 OAuth，而无需在 URL 中包含 Tavily API Key。例如，在你的 `mcp.json` 中：

```json
{
  "mcpServers": {
    "tavily-remote-mcp": {
      "command": "npx mcp-remote https://mcp.tavily.com/mcp",
      "env": {}
    }
  }
}
```

如需清除已存储的 OAuth 凭据并重新认证，运行：

```bash
rm -rf ~/.mcp-auth
```

> **注意**：
> - OAuth 认证是可选的。你仍可以随时使用 API Key 认证，即将 API Key 包含在 URL 查询参数中（`?tavilyApiKey=...`）或在 `Authorization` 头中设置，如上所述。

#### 选择 OAuth 使用的 API Key

OAuth 认证成功之后，你可以通过将 Key 命名为 `mcp_auth_default` 来控制使用哪个 API Key：

- 如果你在**个人账户**中设置了名为 `mcp_auth_default` 的 Key，该 Key 将用于认证流程。
- 如果你属于一个**团队**，且该团队有一个名为 `mcp_auth_default` 的 Key，该 Key 将用于认证流程。
- 如果你同时拥有个人和团队的 `mcp_auth_default` Key，**个人 Key 优先**。
- 如果没有设置 `mcp_auth_default` Key，将使用个人账户中的 `default` Key。如果没有 `default` Key，则使用第一个可用的 Key。

## 本地 MCP 

### 前置条件 🔧

开始前，请确保你拥有：

- [Tavily API Key](https://app.tavily.com/home)
  - 如果没有 Tavily API Key，可以在 [这里](https://app.tavily.com/home) 注册免费账户
- [Claude Desktop](https://claude.ai/download) 或 [Cursor](https://cursor.sh)
- [Node.js](https://nodejs.org/) (v20 或更高版本)
  - 可通过以下命令验证 Node.js 安装：
    - `node --version`
- [Git](https://git-scm.com/downloads)（仅在使用 Git 安装方式时需要）
  - macOS: `brew install git`
  - Linux: 
    - Debian/Ubuntu: `sudo apt install git`
    - RedHat/CentOS: `sudo yum install git`
  - Windows: 下载 [Git for Windows](https://git-scm.com/download/win)

### 使用 NPX 运行

```bash
npx -y tavily-mcp@latest 
```

## 默认参数配置 ⚙️

你可以通过 `DEFAULT_PARAMETERS` 环境变量为 `tavily-search` 工具设置默认参数值。这样无需在每个请求中单独指定这些参数。

### 配置示例

```bash
export DEFAULT_PARAMETERS='{"include_images": true}'
```

## TAVILY_API_KEY 配置

### 单 Key（默认行为）

```bash
export TAVILY_API_KEY="sk-your-api-key"
```

### 多 Key（轮询负载均衡 + 故障转移）

```bash
export TAVILY_API_KEY="sk-key1,sk-key2,sk-key3"
```

当提供多个 Key 时，服务器使用轮询方式将请求分配到各个 Key。如果某个 Key 连续两次返回 429（限流）或 401（无效），该 Key 将被暂时禁用并加入下一个月的 1 号从轮询列表中移除。请求自动使用剩余的健康 Key。禁用的 Key 在下个月 1 号重新启用。

重复 Key 和空白字符会自动处理：`" sk1 , sk1 , sk2 "` 会被去重为 `["sk1", "sk2"]`。

### 客户端使用示例
```json
{
  "mcpServers": {
    "tavily-mcp": {
      "command": "npx",
      "args": ["-y", "tavily-mcp@latest"],
      "env": {
        "TAVILY_API_KEY": "your-api-key-here",
        "DEFAULT_PARAMETERS": "{\"include_images\": true, \"max_results\": 15, \"search_depth\": \"advanced\"}"
      }
    }
  }
}
```

## 标识最终用户（可选）

你可以通过设置 `TAVILY_HUMAN_ID` 环境变量来标识请求的终端用户身份。设置后，Tavily MCP 会在每次 API 调用中将其作为 `X-Human-Id` 头转发，实现按用户维度的数据分析。

这是**完全可选的** — 不设置则行为不变。

```json
{
  "mcpServers": {
    "tavily-mcp": {
      "command": "npx",
      "args": ["-y", "tavily-mcp@latest"],
      "env": {
        "TAVILY_API_KEY": "your-api-key-here",
        "TAVILY_HUMAN_ID": "your-user-id"
      }
    }
  }
}
```

**隐私说明**：Tavily 在服务端对 `human_id` 进行 SHA-256 哈希后再存储，原始值不会被持久化。即便如此，仍建议使用不透明标识符（如内部用户 ID），而非邮箱等个人身份信息。

## 致谢 ✨

- [Model Context Protocol](https://modelcontextprotocol.io) — MCP 规范
- [Anthropic](https://anthropic.com) — Claude Desktop
