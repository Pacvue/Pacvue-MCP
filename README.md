# Pacvue MCP

Query Pacvue ad data and generate reports from any MCP client. We focus on **Cursor, Claude (Code & Desktop), and ChatGPT**, and support any other agent that speaks the [Model Context Protocol](https://modelcontextprotocol.io).

One MCP server, two capabilities, no per-tool wiring.

## What you get

| Capability         | What it's for                                                         | Output                                                        | Limits                                     |
| ------------------ | --------------------------------------------------------------------- | ------------------------------------------------------------- | ------------------------------------------ |
| **Report MCP**     | Ad-hoc one-off report exports — "give me a file I can save / share"   | CSV (or ZIP of CSVs for multi-tab reports) via pre-signed URL | Async, ≤ 100,000 rows, 24h download window |
| **Data Query MCP** | Ad-hoc lookups — "show me the number / a small table / a daily trend" | JSON rows + summary, optional period-over-period              | Sync, ≤ 500 rows, 20s server timeout       |

Both capabilities cover all retailers your Pacvue account has access to (Amazon, Walmart, Instacart, Amazon DSP, Amazon Commerce, Bol, Chewy, Citrus, Commerce-Walmart, Criteo, DoorDash, eBay, Kroger, Mercado, Sam's Club, Target, ...). New retailers and new fields are picked up automatically — no MCP-side changes.

## Endpoint

```
https://mcp.pacvue.com/mcp
```

All 8 tools are exposed under one server entry. You do not configure them individually.

## Authentication

Pacvue MCP reuses Pacvue's existing OAuth 2.0 system — both auth options below resolve to a real Pacvue user, and data access is scoped to that user's Console entitlements.

You'll need from your Pacvue admin (or yourself, if you have access):

- A Pacvue Console account, **and one of**:
  - An **API Token** (for clients that don't support browser-based OAuth), or
  - An MCP client that supports OAuth 2.0 with Authorization Code Flow + PKCE (most modern clients do).

### Option A — API Token (recommended for headless/CLI clients)

1. Sign in to the Pacvue Console.
2. Go to **Settings → MCP → Create API Token**.
3. Give it a name, pick an expiry (max 2 years), click **Create**.
4. **Copy the token immediately.** The full value (`pv_...`) is shown only once. After you close the dialog only the masked prefix remains visible.
5. Paste it into your MCP client config as the value of the `Authorization` header — **no `Bearer` prefix**, just the raw token (see [Client setup](#client-setup)).

To rotate or revoke a token, return to the same page and delete the row. Up to 50 active tokens per user. Tokens are stored hash-only — Pacvue cannot recover a lost token.

### Option B — OAuth 2.0 (browser login)

For clients that support OAuth 2.0 with Dynamic Client Registration (DCR) — Cursor, Claude Code, Claude Desktop, ChatGPT, and most other modern MCP clients.

1. In your client's `mcp.json`, add the server URL **without any `headers` block**.
2. The client opens your default browser on first connection, you log in to Pacvue and click **Authorize**.
3. The browser redirects back to the client over a loopback URL; the client receives an access token + refresh token and the connection is live.
4. Refresh token has a **7-day sliding window** — every refresh resets the TTL. After 7 days of no use, you'll be asked to log in again.

To revoke a session, go to **Settings → Connected Apps** in the Pacvue Console and remove the client. Each device produces its own entry, so you can tell "Cursor on work laptop" from "Cursor on home laptop".

## Client setup

The `Authorization` header takes the **raw token** — no `Bearer` prefix.

### Cursor

Add to `.cursor/mcp.json` (project) or `~/.cursor/mcp.json` (global).

OAuth (recommended):

```json
{
  "mcpServers": {
    "pacvue-mcp": {
      "url": "https://mcp.pacvue.com/mcp"
    }
  }
}
```

API Token:

```json
{
  "mcpServers": {
    "pacvue-mcp": {
      "url": "https://mcp.pacvue.com/mcp",
      "headers": {
        "Authorization": "pv_<your-api-token>"
      }
    }
  }
}
```

### Claude Code

OAuth:

```bash
claude mcp add pacvue-mcp --transport http https://mcp.pacvue.com/mcp
```

API Token:

```bash
claude mcp add pacvue-mcp \
  --transport http \
  --header "Authorization: pv_<your-api-token>" \
  https://mcp.pacvue.com/mcp
```

Or in `.claude/settings.json`:

```json
{
  "mcpServers": {
    "pacvue-mcp": {
      "type": "http",
      "url": "https://mcp.pacvue.com/mcp",
      "headers": {
        "Authorization": "pv_<your-api-token>"
      }
    }
  }
}
```

### Claude Desktop

Edit `claude_desktop_config.json` (`~/Library/Application Support/Claude/` on macOS, `%APPDATA%\Claude\` on Windows):

```json
{
  "mcpServers": {
    "pacvue-mcp": {
      "type": "http",
      "url": "https://mcp.pacvue.com/mcp",
      "headers": {
        "Authorization": "pv_<your-api-token>"
      }
    }
  }
}
```

Drop the `headers` block to use OAuth.

### ChatGPT

ChatGPT uses OAuth only — it does not accept custom HTTP headers, so API Token isn't an option here.

1. Open **Settings → Connectors → Add custom connector**.
2. Server URL: `https://mcp.pacvue.com/mcp`.
3. Sign in via the browser flow when prompted.

### Other MCP clients

Pacvue MCP is a standard streamable-HTTP MCP server, so any compliant client (VS Code GitHub Copilot, Windsurf, Cline, custom in-house agents, ...) can connect with the same two patterns above:

- **OAuth** — point the client at `https://mcp.pacvue.com/mcp` with no headers.
- **API Token** — point the client at the same URL and add an `Authorization` header containing `pv_<your-api-token>` (raw token, no `Bearer` prefix).

If your client expects a different config schema, consult its docs — the URL, the optional header, and the header value are the only Pacvue-side knobs.

## Verify

Fully quit and restart your MCP client (a window reload is not enough for some clients). The `pacvue-mcp` entry should switch to **Connected** and the tool list should appear.

Then ask your agent:

> What Pacvue tools do you have access to?

You should see 8 tools — 5 from Report MCP and 3 from Data Query MCP.

## Tool reference

You don't call these directly — your agent picks them. They're listed here so you know what Pacvue MCP can do, and so you can sanity-check the agent's plan in transcripts.

### Report MCP (5 tools)

| Tool                  | What it does                                                                                                                                 |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `fetch_report_list`   | List the report types available to you, grouped by platform.                                                                                 |
| `fetch_report_schema` | Get the full parameter schema (configs, filters, requirements, columns) for a specific report.                                               |
| `fetch_materials`     | Resolve filter values (profiles, campaigns, tags, ASINs, ...) into the IDs the platform expects.                                             |
| `run_report`          | Submit an async report job. Returns a `taskId`. The agent must show you a human-readable summary and ask for confirmation before submitting. |
| `fetch_report_result` | Poll a `taskId`. Returns `PENDING` / `RUNNING` / `COMPLETED` (with download URL) / `FAILED`.                                                 |

Canonical flow: `fetch_report_list` → `fetch_report_schema` → (`fetch_materials` if filters) → `run_report` → `fetch_report_result`.

### Data Query MCP (3 tools)

| Tool                 | What it does                                                                                                                    |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| `fetch_query_list`   | List retailers + their queryable Dimensions (Profile / Campaign / AdGroup / ASIN / Search Term / ...).                          |
| `fetch_query_schema` | Full capability definition for a retailer: dimensions, metrics, filters, group-by options, date / compare ranges, sort, limits. |
| `execute_query`      | Run the query synchronously. Returns rows + a summary (totals, applied date range, applied group-by, optional comparison).      |

Result rows are nested in **Dimension → Time → Entity** order — when you read a Data Query reply, expect that hierarchy, not flat cross-product rows.

## When to use which

| Question                                                     | Capability                            |
| ------------------------------------------------------------ | ------------------------------------- |
| "Top 10 Campaigns by spend last week?"                       | Data Query                            |
| "Daily ROAS for Campaign A this month?"                      | Data Query                            |
| "Compare this week's top spenders against last week"         | Data Query (with `compareToPrevious`) |
| "Export last month's Amazon Campaigns to a file I can share" | Report                                |
| "Give me a 50,000-row keyword performance dump for Q1"       | Report                                |

Rule of thumb: if the answer fits in chat, use Data Query. If you want a file, use Report.

## Example prompts

```text
Show me last week's top 10 Amazon Campaigns by spend.
```

```text
What was Campaign "Holiday-Q4" daily ROAS this month?
Group by day, include spend / sales / ACoS.
```

```text
Export last month's Walmart Campaigns where Spend > $500.
Columns: campaign name, impressions, clicks, spend, ACoS.
Send me the download link when it's ready.
```

The agent will discover the schema at runtime, ask you to confirm any destructive parameters, and (for reports) poll until the file is ready.

## Troubleshooting

### My browser opened to the Pacvue MCP gateway page

The gateway URL is an MCP endpoint, not a webpage. If it opened in your browser, your client tried to authenticate, failed, and fell back to the URL because no OAuth flow was available. Almost always this means the API token in `mcp.json` is missing, expired, or revoked.

Fix one of two ways and **fully restart your MCP client** afterwards (a window reload is not enough for some clients):

- **Use a token** — make sure `headers.Authorization` in `mcp.json` contains a current `pv_...` token (raw token, no `Bearer` prefix). Generate or rotate one under **Settings → MCP**.
- **Use OAuth instead** — remove the `headers` block from `mcp.json` entirely. The client will show a real **Connect** button that opens the Pacvue login page.

### Connection drops after a week of inactivity

Expected — OAuth refresh tokens have a 7-day sliding window. Reconnect through the browser flow and you're good for another 7 days. To eliminate the prompt entirely, switch to an API token.

### `TIMEOUT` from a Data Query

`execute_query` has a 20-second server-side cap and a 500-row result cap. Either:

- Shorten the date window or reduce group-by nesting (Dimension × Time × Entity grows multiplicatively), or
- Switch to Report MCP for anything that can return more than 500 rows.

### `run_report` failed with a missing-required-field error

The agent skipped a required filter or config. Required fields are enforced upstream by the platform's own API, not by the MCP layer. Re-run the request and tell the agent which platform / profile / time range you want — that's usually the missing piece.

## Security & limits

- All credentials (API tokens, OAuth access + refresh tokens) are stored hash-only on the server.
- Each credential is bound 1:1 to a Pacvue user. Data access is scoped to that user's Console entitlements.
- Issuance, use, and revocation are written to an audit log.
- Revocation is immediate — the next request with a revoked credential is rejected.
- Transport is TLS-only. Do not put `pv_...` tokens into chat history, screenshots, or shared configs.

| Hard cap                | Value                |
| ----------------------- | -------------------- |
| Report rows             | 50,000               |
| Report download URL TTL | 24h                  |
| Report `taskId` TTL     | 24h                  |
| Data Query rows         | 500                  |
| Data Query timeout      | 20s                  |
| API tokens per user     | 50                   |
| API token max lifetime  | 180 days             |
| OAuth refresh token     | 7-day sliding window |
