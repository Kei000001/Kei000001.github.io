---
layout: post
title: "Verifying My MCP Server: From Local Tools to a One-Line uvx Launch"
date: 2026-09-27 09:00:00 +0900
categories: [mcp]
tags: [mcp, fastmcp, uvx, claude-desktop, github-copilot]
---

I built an MCP (Model Context Protocol) server that bundles four tool servers into one, and verified that it works end to end — from local testing to launching it straight from GitHub with a single `uvx` command.

<!--more-->

## What I Built

[My Awesome MCP Server](https://github.com/Kei000001/my-awesome-mcp-server) mounts four FastMCP servers into a single server:

| Server | What it does | Examples |
|---|---|---|
| `calculator` | Basic math | `add`, `divide`, `square_root` |
| `database` | Read-only SQLite analysis | `list_tables`, `execute_safe_query` |
| `external_api` | Weather, news, and IP lookup | `get_weather`, `search_news`, `get_ip_info` |
| `universal` | Web search and Python execution | `web_search`, `execute_python` |

In total it exposes **20 tools, 4 prompts, and 1 resource**. Tool names are namespaced by server (for example, `calculator_add`), so an AI client can tell where each tool comes from.

## How I Verified It

Environment: Ubuntu 22.04, Python 3.13, FastMCP 4.0, uv.

1. **MCP Inspector** — Checked tools, resources, prompts, and log notifications in the browser, over both stdio and Streamable HTTP.
2. **Claude Desktop** — Registered the server and confirmed the model picks the right tool from a plain-language request (for example, "What's 12 × 34?" → `calculator_multiply`).
3. **A custom client** — Called the tools from Python with `fastmcp.Client` to confirm the raw results.
4. **One-line launch from GitHub** — Confirmed that all four servers load when run with:

```bash
uvx --from git+https://github.com/Kei000001/my-awesome-mcp-server my-awesome-mcp-server
```

```text
# startup log (translated from the original Japanese messages)
[loaded] calculator
[loaded] database
[loaded] external_api
[loaded] universal
```

## Pitfalls I Ran Into

- **The package didn't include the servers.** The first `uvx` run started with **0 tools**. Only `src/` is packaged, so servers kept in other folders were missing. I moved them into the package (`src/mcp_learning/servers/`).
- **`ping()` returned "Method not found".** With FastMCP 4.0 and `mcp` 2.x, `client.ping()` failed even though other calls worked. Calling `list_tools()` instead works as a health check.
- **Never print to stdout in a stdio server.** stdout is the MCP message channel, so log messages must go to stderr.
- **A too-simple SQL safety check.** Blocking any query containing `UPDATE` or `CREATE` also blocked harmless columns like `updated_at` and `created_at`. Matching whole words fixed it — and opening the database in read-only mode adds a second layer of protection.
- **Windows paths in sample code.** Paths like `C:\...` silently break on Linux; every config needed POSIX paths.

## What's Next

Next, I plan to connect the same server to GitHub Copilot's agent mode in VS Code, and to explore how MCP can wrap engineering tools so that AI agents can drive real workflows safely.
