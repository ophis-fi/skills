# Ophis MCP Server Installation Guide

This guide is written for an AI agent (such as Cline) installing the Ophis MCP server. Ophis is an intent-based DEX built on CoW Protocol that lets an agent swap tokens onchain: MEV-protected, gasless for the trader, keyless, and non-custodial. The server is remote and HTTP at `https://mcp.ophis.fi/mcp`.

Not to be confused with njayp/ophis, an unrelated Go library for MCP. This is Ophis the DEX at ophis.fi.

## Prerequisites

None to install the server. The Ophis MCP server is keyless, so there is no API key, token, or environment variable to provision. It is a remote HTTP server, so there is nothing to download or build.

To actually place a trade later, the agent needs an EVM wallet it controls (an address plus the ability to sign EIP-712 typed data) and a one-time onchain approval per sell token to the CoW Protocol vault relayer. None of that is needed for installation.

## Installation steps

### 1. Add the server to the MCP settings file

Add the following entry to the host's MCP settings file. Use the path for your client:

- Cline (VS Code extension): `~/Library/Application Support/Code/User/globalStorage/saoudrizwan.claude-dev/settings/cline_mcp_settings.json`
- Roo Code (VS Code extension): `~/Library/Application Support/Code/User/globalStorage/rooveterinaryinc.roo-cline/settings/mcp_settings.json`
- Claude Desktop: `~/Library/Application Support/Claude/claude_desktop_config.json` (use the stdio bridge in step 2)
- Cursor: `~/.cursor/mcp.json` or project `.cursor/mcp.json`

Merge this into the `mcpServers` object (do not overwrite existing servers):

```json
{
  "mcpServers": {
    "ophis": {
      "type": "streamableHttp",
      "url": "https://mcp.ophis.fi/mcp",
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

Keep `autoApprove` empty. `submit_order` places a real, executable onchain trade and must always require explicit user approval.

### 2. If the client cannot open a remote HTTP server (stdio only)

Some clients, including Claude Desktop, only launch stdio commands. For those, bridge the remote server with `mcp-remote`:

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

### 3. In Claude Code, prefer the full plugin

If the host is Claude Code, install the plugin instead, which adds both the MCP server and the `ophis-swap` workflow skill:

```
/plugin marketplace add ophis-fi/skills
/plugin install ophis@ophis-fi
```

## Available tools

After installation the agent has 12 tools:

- `parse_intent`: turn plain English ("swap 100 USDC for ETH on Base") into sell token, buy token, amount, and chain.
- `resolve_token`: map a symbol to its canonical address. Fails closed, so a spoofed token with a familiar symbol is not returned.
- `list_chains`: the authoritative live (tradeable) and paused chain sets.
- `get_quote`: a best-execution quote for a sell or buy.
- `expected_surplus`: compares an Ophis quote against a public aggregator and returns the difference in basis points.
- `build_order`: returns an unsigned, bounded order plus the EIP-712 typed data to sign.
- `submit_order`: relays a signed order to the orderbook. This is the only state-changing tool.
- `lookup_tier`: a wallet's fee-rebate tier and rebate percentage.
- `get_balances`, `get_portfolio`: native and ERC-20 balances on one chain or across chains.
- `get_gas`: current gas price (informational; trades are gasless for the trader).
- `get_token_chart`: OHLCV price history.

The flow is non-custodial: `build_order` returns an unsigned order, the agent signs it with its own wallet, and `submit_order` relays it. The server never sees the private key, and the receiver is pinned to the signer.

## Verify the installation

1. Restart the client so it loads the new server.
2. Ask the agent to call `list_chains`. A successful call returns the tradeable chain set (Ethereum, Optimism, BNB Chain, Gnosis, Polygon, Base, Arbitrum, Avalanche, Plasma, Ink, Linea), which confirms the server is connected. No key is required, so this should succeed immediately.

## Troubleshooting

- If the server shows as connected but tool calls fail, confirm the URL is exactly `https://mcp.ophis.fi/mcp` and that the client is configured for an HTTP (streamable) transport, not stdio. A plain browser GET to that URL returns HTTP 406, which is expected and does not mean the server is down.
- If your client cannot use a remote HTTP server, use the `mcp-remote` bridge in step 2.
- The server is keyless. If a setup step asks for an API key or token, it is not needed; leave it blank.

## Links

- App: https://swap.ophis.fi
- Site: https://ophis.fi
- Contact: contact@ophis.fi
