# n0brains MCP Server

**Ask n0brains first.** Audited crypto market intelligence for AI agents — served
as a hosted [Model Context Protocol](https://modelcontextprotocol.io) server.

- **Endpoint:** `https://api.n0brains.com/mcp/` (Streamable HTTP)
- **Auth:** API key via `X-API-Key` or `Authorization: Bearer` — [free keys here](https://n0brains.com/signup)
- **Registry:** [`com.n0brains/mcp`](https://registry.modelcontextprotocol.io/v0/servers?search=n0brains) (official MCP Registry)
- **Receipts:** every verdict this server hands out is logged and publicly scored at [n0brains.com/proof](https://n0brains.com/proof)

> This repo is the public connection guide. The server itself is hosted —
> nothing to install, nothing to run locally.

## What your agent gets

### `check_trade` — the flagship
Submit a trade idea (asset, side, and optionally entry / stop / target /
leverage / horizon). Get back a graded conditions assessment (**A–F**) built
from data a chart can't show:

- **Positioning crowding** — funding z-score: are you joining the crowded side?
- **Scheduled event risk** — high-impact macro events inside your horizon vs your stop distance
- **Liquidation math** — is your liquidation price within one ordinary day's volatility?
- **Stop sanity** — wrong-side stops, stops inside the noise band
- **Proven-edge conflicts** — measured, statistically proven edges pointing the other way
- **Falsifiers** — exactly what would change the grade, so your agent can monitor

Every grade is logged when issued and resolved at its horizon against real
prices. Grade calibration (do A-grades actually beat F-grades?) publishes on
[/proof](https://n0brains.com/proof).

### Everything else (33 tools)
Typed signals with per-type win rates (failures included), own DEX liquidation
maps, support/resistance levels, market regime, macro bias, economic calendar,
options analytics, cross-asset flows, market analogs, and more —
[full docs](https://n0brains.com/docs).

## Tiers

| | Free (any key) | Pro ($39.99/mo) |
|---|---|---|
| Tools | 13 (incl. `check_trade`) | all 33 |
| `check_trade` | 3/day | unlimited |
| Signals | 15-min delayed | real-time |
| Rate limit | 60/min | 600/min |

Free tools: `check_trade`, `list_signals`, `get_signal`, `get_signals_since`,
`get_levels`, `get_market_opens`, `get_performance`, `get_asset_class_proof`,
`get_tier_performance`, `get_market_regime`, `get_macro`,
`get_economic_calendar`, `health`.

## Connect

### Claude Desktop / Claude Code
```json
{
  "mcpServers": {
    "n0brains": {
      "type": "http",
      "url": "https://api.n0brains.com/mcp/",
      "headers": { "X-API-Key": "intel_sk_YOUR_KEY" }
    }
  }
}
```

Older clients without native Streamable HTTP support can bridge via
[`mcp-remote`](https://github.com/geelen/mcp-remote):
```json
{
  "mcpServers": {
    "n0brains": {
      "command": "npx",
      "args": ["mcp-remote", "https://api.n0brains.com/mcp/",
               "--header", "X-API-Key: intel_sk_YOUR_KEY"]
    }
  }
}
```

### Cursor / Cline / anything MCP
Point the client at `https://api.n0brains.com/mcp/` with the `X-API-Key`
header. `initialize` and `tools/list` are open (no key) for catalog discovery.

### Quick smoke test
```bash
curl -s -X POST https://api.n0brains.com/mcp/ \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -H "X-API-Key: intel_sk_YOUR_KEY" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call",
       "params":{"name":"check_trade",
                 "arguments":{"asset":"BTC","side":"long","leverage":10}}}'
```

## Honesty notes

Conditions assessments are analytical, not financial advice. We publish our
full track record — wins, losses, and demoted signal types — at
[n0brains.com/proof](https://n0brains.com/proof), including the calibration of
the very grades this server hands out. Past performance does not guarantee
future results.

---
**Links:** [Website](https://n0brains.com) · [Docs](https://n0brains.com/docs) ·
[Proof](https://n0brains.com/proof) · [Pricing](https://n0brains.com/pricing) ·
[X](https://x.com/n0brainsai) · [Telegram](https://t.me/n0brainssignals)
