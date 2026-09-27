---
layout: page
title: Projects
permalink: /projects/
---

A curated list of things I have built, reproduced, or improved. Each entry links to the code and, where available, a write-up.

## LLM Agents

### [My Awesome MCP Server](https://github.com/Kei000001/my-awesome-mcp-server)

An MCP (Model Context Protocol) server that mounts four tool servers into one: a calculator, read-only SQLite analysis, external APIs (weather, news, IP lookup), and web search with sandboxed Python execution — 20 tools in total.

- **Highlights:** one-line launch from GitHub with `uvx`; verified with MCP Inspector, Claude Desktop, and a custom client
- **Stack:** Python, FastMCP, uv
- **Write-up:** [Verifying My MCP Server]({% post_url 2026-09-27-verifying-my-mcp-server %})

---

More projects on robot learning (VLA fine-tuning), paper reimplementations, and Kaggle are on the way. See [Topics]({{ "/topics/" | relative_url }}) for all posts by category.
