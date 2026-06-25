# Ophis plugins for Claude Code

Official [Ophis](https://ophis.fi) plugin marketplace for Claude Code. Ophis is an intent-based DEX built on CoW Protocol: MEV-protected, gasless for the trader, and keyless.

## Install

```
/plugin marketplace add ophis-fi/skills
/plugin install ophis@ophis-fi
```

## Plugins

| Plugin | What it does |
| --- | --- |
| `ophis` | Swap tokens onchain via the Ophis MCP server (quote, build, submit, balances, portfolio, prices, gas, fee-rebate tier) plus the `ophis-swap` workflow skill. |

## About the `ophis` plugin

Installing it wires up the Ophis MCP server (`https://mcp.ophis.fi/mcp`) and adds a skill that teaches the agent how to trade safely. The swap path is non-custodial: the server builds an unsigned order, the agent signs it with its own wallet, and the server relays the signed order. The receiver is always the signer.

See [`plugins/ophis/README.md`](plugins/ophis/README.md) for details.

## Links

- App: https://swap.ophis.fi
- Site: https://ophis.fi
- Contact: contact@ophis.fi

## License

MIT
