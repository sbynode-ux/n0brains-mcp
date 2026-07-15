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

### `log_trade` / `close_trade` / `get_journal` — the trade journal (Pro)
Your agent says "in at 64,635" and the journal does the rest. `log_trade`
snapshots full entry conditions automatically (grade, flags, regime — the
same read as `check_trade`), and `close_trade` resolves the outcome from real
candles: return %, R multiple vs your initial stop, MAE/MFE (worst drawdown /
best unrealized gain while open), and whether your stop or target level
actually traded — wick-accurate. `get_journal` returns open positions with
live unrealized PnL, closed history, and a personal calibration block:
**your realized R per entry grade** — where your entries are actually good.

Historical backfill: pass `opened_at` / `closed_at` (epoch seconds) to import
past trades. Backfilled entries are graded by **your own `check_trade`
nearest the fill** — the read you actually got at the time, never re-graded
on today's tape; no matched check means the entry stays ungraded, so your
calibration never lies to you. A 5-minute watchdog flags open trades whose
stop or target level trades. The journal is private to your account.

### `get_price` / `get_prices` — canonical price (free)
THE live exchange mid every other tool's spot should agree with. If any
payload's embedded price disagrees materially with `get_price`, that payload
is stale — discount it. Built so your agent never has to trust a stale anchor.

### Everything else (38 tools total)
Typed signals with per-type win rates (failures included), own DEX liquidation
maps, support/resistance levels, market regime, macro bias, economic calendar,
options analytics with positioning coverage grades, cross-asset flows, market
analogs, mindshare, trade plans, and more —
[full docs](https://n0brains.com/docs).

## Tiers

| | Free (any key) | Pro ($39.99/mo) |
|---|---|---|
| Tools | 14 (incl. `check_trade`) | all 38 |
| `check_trade` | 3/day | unlimited |
| Signals | 15-min delayed | real-time |
| Trade journal | — | included |
| Rate limit | 60/min | 600/min |

Free tools: `check_trade`, `list_signals`, `get_signal`, `get_signals_since`,
`get_levels`, `get_market_opens`, `get_price`, `get_prices`,
`get_performance`, `get_asset_class_proof`, `get_market_regime`, `get_macro`,
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

### claude.ai (custom connector)
claude.ai connectors can't send custom headers — pass the key as a query
parameter instead (headers win if both are present):
```
https://api.n0brains.com/mcp?api_key=intel_sk_YOUR_KEY
```
Settings → Connectors → Add custom connector → paste the URL. Note: URLs can
end up in logs and screenshots — treat a query-param key like any secret, and
rotate it from your dashboard if it leaks. If new server tools don't appear
in existing chats, reconnect the connector and start a fresh chat (clients
cache the tool catalog).

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
