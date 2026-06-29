# Distribution

How to install Ophis across agent ecosystems, and where it is listed. Ophis is an intent-based DEX built on CoW Protocol: MEV-protected, gasless for the trader, keyless, and non-custodial. The product ships in two parts: a Claude Code plugin (this repo) that bundles the MCP server plus the `ophis-swap` skill, and the Ophis MCP server on its own, which is remote, HTTP, and keyless at `https://mcp.ophis.fi/mcp`.

> Not to be confused with [njayp/ophis](https://github.com/njayp/ophis), an unrelated Go library for MCP. This is Ophis the DEX at [ophis.fi](https://ophis.fi).

## Where Ophis is listed

| Catalog | Identifier | Link |
| --- | --- | --- |
| Claude Code plugin marketplace | `ophis@ophis-fi` (marketplace `ophis-fi`) | this repo, github.com/ophis-fi/skills |
| Official MCP Registry | `fi.ophis/mcp` (namespace `fi.ophis`, DNS-verified to ophis.fi) | https://registry.modelcontextprotocol.io/v0/servers?search=ophis |

The MCP Registry entry is the canonical source feed. Downstream MCP catalogs that mirror the registry pick it up from there. The plugin marketplace is this GitHub repository, added directly by name.

## Install matrix

| Client | Method | One step |
| --- | --- | --- |
| Claude Code | Plugin (MCP + skill) | `/plugin marketplace add ophis-fi/skills` then `/plugin install ophis@ophis-fi` |
| Claude Code | MCP only | `claude mcp add --transport http ophis https://mcp.ophis.fi/mcp` |
| Cursor | Deeplink or `mcp.json` | Add to Cursor button below |
| VS Code / Copilot | Badge or `.vscode/mcp.json` | Install in VS Code button below |
| Codex | `~/.codex/config.toml` | `[mcp_servers.ophis]` table below |
| Cline / Roo Code | `cline_mcp_settings.json` | `mcpServers` map below, plus `llms-install.md` |
| Windsurf | `mcp_config.json` | `mcpServers` map below |
| Claude Desktop | `claude_desktop_config.json` via mcp-remote | `mcp-remote` bridge below |

### Claude Code

```
/plugin marketplace add ophis-fi/skills
/plugin install ophis@ophis-fi
```

The plugin is the only path that also installs the `ophis-swap` skill. Every other client below installs the MCP server alone, which gives the agent the 12 tools but not the workflow guidance.

### Cursor

[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](cursor://anysphere.cursor-deeplink/mcp/install?name=ophis&config=eyJ1cmwiOiJodHRwczovL21jcC5vcGhpcy5maS9tY3AifQ==)

Manual, `~/.cursor/mcp.json` or `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "ophis": {
      "url": "https://mcp.ophis.fi/mcp"
    }
  }
}
```

### VS Code / GitHub Copilot

[![Install in VS Code](https://img.shields.io/badge/VS_Code-Install_Server-0098FF?style=flat-square&logo=visualstudiocode&logoColor=white)](https://vscode.dev/redirect/mcp/install?name=ophis&config=%7B%22type%22%3A%20%22http%22%2C%22url%22%3A%20%22https%3A%2F%2Fmcp.ophis.fi%2Fmcp%22%7D)

Manual, `.vscode/mcp.json` (top-level `servers` key):

```json
{
  "servers": {
    "ophis": {
      "type": "http",
      "url": "https://mcp.ophis.fi/mcp"
    }
  }
}
```

### Codex

`~/.codex/config.toml`:

```toml
[mcp_servers.ophis]
url = "https://mcp.ophis.fi/mcp"
```

### Cline, Roo Code, Windsurf, and other MCP clients

Standard `mcpServers` map with the Streamable HTTP transport:

```json
{
  "mcpServers": {
    "ophis": {
      "type": "streamableHttp",
      "url": "https://mcp.ophis.fi/mcp"
    }
  }
}
```

Cline can also read [`llms-install.md`](llms-install.md) in this repo to configure the server for you.

### Claude Desktop (stdio bridge)

Claude Desktop expects a stdio command, so bridge the remote server with `mcp-remote` in `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "ophis": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.ophis.fi/mcp"]
    }
  }
}
```

## What you get

12 tools: `parse_intent`, `resolve_token`, `list_chains`, `get_quote`, `expected_surplus`, `build_order`, `submit_order`, `lookup_tier`, `get_balances`, `get_portfolio`, `get_gas`, `get_token_chart`. `submit_order` is the only state-changing tool; everything else reads or builds. `resolve_token` maps a symbol to its canonical address and fails closed, so an agent never swaps into a token that merely spoofs a well-known symbol. No API key is required at any step.

## Supported chains

Trading is live on Ethereum (1), Optimism (10), BNB Chain (56), Gnosis (100), Polygon (137), Base (8453), Arbitrum (42161), Avalanche (43114), Plasma (9745), Ink (57073), and Linea (59144). `list_chains` reports the authoritative live set at runtime.

## Links

- App: https://swap.ophis.fi
- Site: https://ophis.fi
- Contact: contact@ophis.fi
- MCP server source: https://github.com/ophis-fi/ophis (subfolder `apps/mcp-server`)
