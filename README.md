# Ophis plugins for Claude Code

[![License: MIT](https://img.shields.io/github/license/ophis-fi/skills?style=flat-square)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/ophis-fi/skills?style=flat-square)](https://github.com/ophis-fi/skills/stargazers)
[![MCP Registry](https://img.shields.io/badge/MCP_Registry-fi.ophis%2Fmcp-1f6feb?style=flat-square)](https://registry.modelcontextprotocol.io/v0/servers?search=ophis)
[![Claude Code plugin](https://img.shields.io/badge/Claude_Code-ophis%40ophis--fi-d97757?style=flat-square)](https://github.com/ophis-fi/skills)

Official [Ophis](https://ophis.fi) plugin marketplace for Claude Code. Ophis is an intent-based DEX built on CoW Protocol: MEV-protected, gasless for the trader, keyless, and non-custodial. Trading is live on 11 EVM chains.

> Not to be confused with [njayp/ophis](https://github.com/njayp/ophis), an unrelated Go library for MCP. This is Ophis the DEX at [ophis.fi](https://ophis.fi).

## Install in Claude Code

```
/plugin marketplace add ophis-fi/skills
/plugin install ophis@ophis-fi
```

This wires up the Ophis MCP server (`https://mcp.ophis.fi/mcp`) and adds the `ophis-swap` skill, which teaches the agent the safe order of operations for a trade. Then ask the agent to swap, for example: "swap 100 USDC for ETH on Base."

You can also add the MCP server only, without the skill:

```
claude mcp add --transport http ophis https://mcp.ophis.fi/mcp
```

## Use the Ophis MCP server in other agents

The Ophis MCP server is remote, HTTP, and keyless, so the same endpoint works in any MCP client. The `ophis-swap` skill is specific to Claude Code; the MCP tools are universal.

### Cursor

[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](cursor://anysphere.cursor-deeplink/mcp/install?name=ophis&config=eyJ1cmwiOiJodHRwczovL21jcC5vcGhpcy5maS9tY3AifQ==)

Or add to `~/.cursor/mcp.json` (global) or `.cursor/mcp.json` (per project):

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
[![Install in VS Code Insiders](https://img.shields.io/badge/VS_Code_Insiders-Install_Server-24bfa5?style=flat-square&logo=visualstudiocode&logoColor=white)](https://insiders.vscode.dev/redirect/mcp/install?name=ophis&config=%7B%22type%22%3A%20%22http%22%2C%22url%22%3A%20%22https%3A%2F%2Fmcp.ophis.fi%2Fmcp%22%7D)

Or add to `.vscode/mcp.json` (note the top-level `servers` key):

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

Add to `~/.codex/config.toml`:

```toml
[mcp_servers.ophis]
url = "https://mcp.ophis.fi/mcp"
```

### Other MCP clients (Cline, Roo Code, Windsurf, Claude Desktop)

Use the standard `mcpServers` map:

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

For a stdio-only client, bridge with `npx -y mcp-remote https://mcp.ophis.fi/mcp`. See [DISTRIBUTION.md](DISTRIBUTION.md) for the full per-ecosystem matrix.

## Plugins

| Plugin | What it does |
| --- | --- |
| `ophis` | Swap tokens onchain via the Ophis MCP server (quote, build, submit, balances, portfolio, prices, gas, fee-rebate tier) plus the `ophis-swap` workflow skill. |

## About the `ophis` plugin

Installing it wires up the Ophis MCP server (`https://mcp.ophis.fi/mcp`) and adds a skill that teaches the agent how to trade safely. The swap path is non-custodial: the server builds an unsigned order, the agent signs it with its own wallet, and the server relays the signed order. The receiver is always the signer.

The 12 tools are `parse_intent`, `resolve_token`, `list_chains`, `get_quote`, `expected_surplus`, `build_order`, `submit_order`, `lookup_tier`, `get_balances`, `get_portfolio`, `get_gas`, and `get_token_chart`. `submit_order` is the only state-changing tool; `resolve_token` maps a symbol to its canonical address and fails closed, so an agent never swaps into a token that merely spoofs a well-known symbol.

See [`plugins/ophis/README.md`](plugins/ophis/README.md) for details.

## Supported chains

Trading is live on Ethereum (1), Optimism (10), BNB Chain (56), Gnosis (100), Polygon (137), Base (8453), Arbitrum (42161), Avalanche (43114), Plasma (9745), Ink (57073), and Linea (59144). `list_chains` reports the authoritative live set at runtime.

## Links

- App: https://swap.ophis.fi
- Site: https://ophis.fi
- Contact: contact@ophis.fi
- MCP server source: https://github.com/ophis-fi/ophis (subfolder `apps/mcp-server`)
- MCP Registry: `fi.ophis/mcp`

## License

MIT
