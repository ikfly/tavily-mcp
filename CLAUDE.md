# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Tavily MCP is a TypeScript-based Model Context Protocol server that wraps Tavily's AI-powered web search API. It exposes five tools — `tavily_search`, `tavily_extract`, `tavily_crawl`, `tavily_map`, and `tavily_research` — to Claude Code, Cursor, Claude Desktop, and other MCP clients. The entire server is a single file: [src/index.ts](src/index.ts).

## Key Files

- [src/index.ts](src/index.ts) — Single-file server: `TavilyClient` class, tool definitions, API calls, result formatters, CLI entrypoint
- [package.json](package.json) — npm package config; bin entry points to `build/index.js`
- [tsconfig.json](tsconfig.json) — ES2022 target, Node16 module resolution, strict mode, output to `./build`
- [Dockerfile](Dockerfile) — Multi-stage Alpine build (Node 22)
- [smithery.yaml](smithery.yaml) — Smithery deployment descriptor

## Common Commands

```bash
# Build (also run via `npm prepare` on install)
npm run build

# Watch mode for development
npm run watch

# Run the MCP inspector (interactive debugging)
npm run inspector

# Run directly (after build)
node build/index.js

# List available tools (CLI flag)
node build/index.js --list-tools

# Docker build and run
docker build -t tavily-mcp .
docker run --env TAVILY_API_KEY=your-key tavily-mcp
```

## Architecture

- **Single-file design**: Everything lives in [src/index.ts](src/index.ts). The `TavilyClient` class encapsulates the MCP server lifecycle, tool registration, API client (axios), and result formatting.
- **MCP protocol**: Uses `@modelcontextprotocol/sdk` (v1.26.0). The server communicates over stdio (`StdioServerTransport`) and registers two request handlers: `ListToolsRequestSchema` and `CallToolRequestSchema`.
- **Authentication**: Supports two modes:
  - **API key mode** (default): `TAVILY_API_KEY` env var → `Authorization: Bearer` header
  - **Keyless mode**: No API key → `X-Tavily-Access-Mode: keyless` header; search/extract work, other tools require a key
- **Default parameters**: `DEFAULT_PARAMETERS` env var (JSON string) merges defaults into every `tavily_search` call.
- **Tavily API endpoints**: All five tools map to corresponding `api.tavily.com` REST endpoints. The `research` tool uses an async poll pattern (initial POST + polling GET with exponential backoff).
- **Error handling**: Axios errors are caught and formatted with docs links. Keyless rate-limit envelopes are detected and rendered as user-friendly text.
- **CLI**: `yargs` parses `--list-tools` and `--help` flags before starting the server.

## Environment Variables

| Variable | Purpose |
|----------|---------|
| `TAVILY_API_KEY` | Tavily API key (required for most tools; search/extract work keyless) |
| `TAVILY_HUMAN_ID` | Optional end-user identifier (forwarded as `X-Human-Id` header, hashed server-side) |
| `DEFAULT_PARAMETERS` | JSON string of default search parameters (e.g. `{"include_images": true}`) |

## Testing

This project has no built-in test suite. To verify changes:

1. Build: `npm run build`
2. Set `TAVILY_API_KEY` and run: `node build/index.js`
3. Test tools via MCP Inspector: `npm run inspector`
4. Or use the CLI flag: `node build/index.js --list-tools`
