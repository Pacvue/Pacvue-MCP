# Pacvue MCP

Generate Pacvue ad reports from any MCP client. We focus on **Cursor, Claude (Code & Desktop), and ChatGPT**, and support any other agent that speaks the [Model Context Protocol](https://modelcontextprotocol.io).

One MCP server, no per-tool wiring.

## What you get

| Capability         | What it's for                                                       | Output                                                        | Limits                                            |
| ------------------ | ------------------------------------------------------------------- | ------------------------------------------------------------- | ------------------------------------------------- |
| **Report MCP**     | Ad-hoc one-off report exports — "give me a file I can save / share" | CSV (or ZIP of CSVs for multi-tab reports) via pre-signed URL | Async, ≤ 150,000 rows, 24h download window         |
| **Data Query MCP** | Inline analytical queries — "show me the numbers in chat"           | Inline JSON rows in the agent response                        | Synchronous           |
| **SOV Query MCP**  | Share of Voice in chat — brand / keyword / ASIN tabs                | Inline paginated JSON tables                                  | Synchronous |

Report MCP covers all retailers your Pacvue account has access to. New retailers and new fields are picked up automatically — no MCP-side changes. Data Query MCP and SOV Query MCP run on the same retailer footprint and the same Console permission model — entitlements decide which tools and platforms appear in the agent's list.

## Choosing the right toolset

| User intent                  | Toolset                  | When to use                                                          |
| ---------------------------- | ------------------------ | -------------------------------------------------------------------- |
| Download a CSV/ZIP report    | Report MCP               | "Send me the file", scheduled exports, large historical pulls        |
| Ad-hoc metrics in chat       | Data Query MCP           | "Show me the numbers", quick analysis, follow-up questions in thread |
| Share of Voice table in chat | SOV Query MCP            | Competitive SOV brand/keyword/ASIN views without leaving the agent   |
| Bulk SOV export to file      | Report MCP (`SOVReport`) | Spreadsheet hand-off, multi-tab SOV ZIP                              |

> One toolset per task. Pick a flow and stay in it — agents shouldn't mix Report calls with Data Query or SOV Query calls in the same job.

## Retailer support

The matrix below shows which retailers each toolset covers. The actual list returned to your agent is scoped to your Pacvue Console entitlements — you only see retailers and tools you have access to.

> Specific report types, levels, and schemas are discovered at runtime via `fetch_report_list`, `fetch_query_list`, and `fetch_query_schema`. There's no static list to keep in sync — when Pacvue adds a retailer or a field, your agent picks it up on the next call.

| Retailer                      | Report MCP (export) | Data Query MCP (in-chat) | SOV Query MCP (in-chat) |
| ----------------------------- | ------------------- | ------------------------ | ----------------------- |
| Amazon — Sponsored Ads        | ✓                   | ✓                        | ✓                       |
| Amazon — DSP                  | ✓                   | ✓                        | —                       |
| Amazon — Commerce (Vendor 1P) | ✓                   | ✓                        | —                       |
| Amazon — Commerce (Seller 3P) | ✓                   | ✓                        | —                       |
| Walmart — Sponsored Ads       | ✓                   | ✓                        | ✓                       |
| Walmart — Commerce (Vendor 1P) | —                   | ✓                        | —                       |
| Instacart                     | ✓                   | ✓                        | ✓                       |
| Target                        | ✓                   | ✓                        | ✓                       |
| Kroger                        | ✓                   | ✓                        | ✓                       |
| Criteo                        | ✓                   | ✓                        | ✓                       |
| Citrus                        | ✓                   | ✓                        | ✓                       |
| Bol                           | ✓                   | ✓                        | ✓                       |
| Chewy                         | ✓                   | ✓                        | ✓                       |
| Sam's Club                    | ✓                   | ✓                        | ✓                       |
| DoorDash                      | ✓                   | ✓                        | ✓                       |

### Notes per toolset

- **Report MCP** — covers the retailers marked ✓ above (14 product lines). Amazon Commerce reports are split between Vendor (1P) and Seller (3P) via the `channel` field on each report entry. Walmart Commerce is **not** available in Report MCP. Use `fetch_report_list` to see the full report catalog for a retailer at runtime.
- **Data Query MCP** — wired for 15 platforms. Scope keys differ by retailer:
  - Standard ads & commerce → `profileIds` (resolve via `materialType=profile`)
  - Amazon DSP → `advertiserIds` (resolve via `materialType=advertiser`)
  - Amazon Commerce → split into `commerce-amazon-vendor` and `commerce-amazon-seller` platform keys (different from the Report MCP `commerce` + `channel` model — pick the right key)
  - Walmart Commerce → `commerce-walmart-vendor` (Vendor 1P only); resolve scope via `materialType=vendor_account` → `profileIds`. See [Walmart Commerce Data Query scope](#walmart-commerce-data-query-scope) below.
- **SOV Query MCP** — 11 platforms. Amazon and Walmart support full `deviceMode`; the other nine are `Aggregated` only. Keyword tag filters work on `amazon` / `walmart` / `instacart` / `criteo` (brand & keyword tabs). Walmart-only `zip_code` filter; `instacart` / `criteo` / `citrus` / `doordash` use `store` (retailerIds).

### Amazon Commerce Data Query scope

What's available in Data Query per Amazon Commerce channel:


| Category              | Vendor (1P) | Seller (3P)             |
| --------------------- | ----------- | ----------------------- |
| Sales                 | ✓           | ✓                       |
| Ads                   | ✓           | ✓                       |
| Margin / COGS         | ✓           | —                       |
| Inventory             | ✓           | ✓                       |
| FBA inventory detail  | —           | ✓                       |
| Content score         | ✓           | ✓                       |
| Buy Box / pricing     | ✓           | ✓                       |
| Promotion             | ✓           | ✓                       |
| Coupon                | ✓           | ✓                       |
| Alerts                | ✓           | ✓                       |
| PO (purchase orders)  | ✓           | —                       |
| BSR ranking           | ✓           | ✓                       |
| Real-time             | ✓           | ✓ (hourly granularity)  |


> Vendor (1P) and Seller (3P) route to different backend services — `commerce-amazon-vendor` and `commerce-amazon-seller`. Pick the right platform key when calling `execute_query`.

### Walmart Commerce Data Query scope

Walmart Commerce is wired into Data Query for **Vendor (1P) only**, under the `commerce-walmart-vendor` platform key. What's available:


| Category          | Vendor (1P) | Seller (3P) |
| ----------------- | ----------- | ----------- |
| Sales             | ✓           | —           |
| Inventory         | ✓           | —           |
| Buy Box / pricing | ✓           | —           |


> Resolve scope via `materialType=vendor_account` → pass IDs in `execute_query.profileIds`. Only Vendor (1P) is supported — Walmart Seller (3P) commerce is not available in either Data Query or Report MCP.

## Endpoint

```
https://mcp.pacvue.com/mcp
```

All tools — Report MCP, Data Query MCP, and SOV Query MCP — are exposed under one server entry. You do not configure them individually. The agent will only see the toolsets your account is entitled to.

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

### Claude Desktop & Claude (claude.ai)

**Recommended: Custom Connector (OAuth, no config files)**

Both Claude Desktop and Claude on the web support adding remote MCP servers as **Custom Connectors** — no `mcp.json`, no Node.js, no `npx`. This is the simplest and most reliable path, and it's what we recommend.

1. Open **Settings → Connectors → Add custom connector**.

- In Claude Desktop: click your name in the lower-left → **Settings** → **Connectors**.
- In Claude on the web: profile menu → **Settings** → **Connectors**.

2. Fill in:

- **Name:** `Pacvue MCP` (or whatever you like)
- **Remote MCP server URL:** `https://mcp.pacvue.com/mcp`

3. Click **Add**, then **Connect**. A browser tab opens for Pacvue OAuth — sign in and click **Authorize**.
4. Back in Claude, the connector flips to **Connected** and the 5 Report MCP tools become available immediately. No restart needed.

This uses OAuth, so connections drop after 7 days of inactivity and you'll be asked to re-authorize. To revoke, remove the connector in the same UI, or remove the device entry under **Settings → Connected Apps** in the Pacvue Console.

> **Why this is preferred:** Custom Connectors talk to the MCP endpoint natively over HTTP. The `mcp-remote` bridge (below) is only needed if you specifically need API Token auth, or if your Claude Desktop version is too old to expose the Connectors UI.

---

**Alternative: `mcp-remote` bridge (required for API Token auth)**

If you need to authenticate with an API Token (headless usage, CI, shared machines, or any case where browser OAuth isn't an option), use the `[mcp-remote](https://www.npmjs.com/package/mcp-remote)` stdio bridge.

**Prerequisites:** [Node.js](https://nodejs.org/) (LTS, includes `npx`) installed and on your `PATH`.

Edit `claude_desktop_config.json`:

- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`

You can open it directly from **Claude → Settings → Developer → Edit Config**.

API Token, macOS / Linux:

```json
{
  "mcpServers": {
    "pacvue-mcp": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://mcp.pacvue.com/mcp",
        "--header",
        "Authorization: pv_<your-api-token>"
      ]
    }
  }
}
```

API Token, Windows:

```json
{
  "mcpServers": {
    "pacvue-mcp": {
      "command": "npx.cmd",
      "args": [
        "-y",
        "mcp-remote",
        "https://mcp.pacvue.com/mcp",
        "--header",
        "Authorization: pv_<your-api-token>"
      ]
    }
  }
}
```

> **Note (Windows):** Use `npx.cmd` rather than `npx` — Claude Desktop on Windows spawns the command directly without a shell, so the `.cmd` extension is required.

After saving the config, **fully quit and relaunch Claude Desktop** (close the tray icon too — a window close is not enough). The `pacvue-mcp` entry should appear under **Settings → Developer** as **running**, and the 5 Report MCP tools should be available in chat.

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

You should see up to **12 tools** across three toolsets:

- **Report MCP** — 5 tools
- **Data Query MCP** — 5 tools
- **SOV Query MCP** — 2 tools

Tools you don't have entitlements for won't appear in the agent's tool list — that's expected, not a misconfiguration.

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

### Data Query MCP (5 tools)

| Tool                    | What it does                                                                                                  |
| ----------------------- | ------------------------------------------------------------------------------------------------------------- |
| `list_query_platforms`  | List the query platforms available to you (`amazon-ads`, `amazon-dsp`, `walmart`, ...). No arguments.         |
| `fetch_query_list`      | Get the level hierarchy + attribute/tag tables for one platform (e.g. profile → campaign → adgroup → target). |
| `fetch_query_schema`    | Self-describing schema for a `(platform, level)` — dimensions, measures, filter operators, granularities.     |
| `fetch_query_materials` | Resolve names to IDs (profile, advertiser, campaign, tag, vendor/seller account, ...).                        |
| `execute_query`         | Run the query and return columns + rows (JSON) inline in the agent response.                                  |

Canonical flow: `list_query_platforms` → `fetch_query_list` → `fetch_query_schema` → (`fetch_query_materials` for scope/filter IDs) → `execute_query`.

**Wired query platforms (15):** `amazon-ads`, `amazon-dsp`, `walmart`, `instacart`, `target`, `kroger`, `criteo`, `citrus`, `bol`, `chewy`, `samsclub`, `doordash`, `commerce-amazon-vendor`, `commerce-amazon-seller`, `commerce-walmart-vendor`.

**Scope rules** — `execute_query` requires a non-empty scope, resolved via `fetch_query_materials`:

- **Standard ads & commerce** → `materialType=profile` → pass IDs in `execute_query.profileIds`.
- **Amazon DSP** → `materialType=advertiser` → pass IDs in `execute_query.advertiserIds`.
- **Amazon Commerce** → `materialType=vendor_account` (1P) or `seller_account` (3P), IDs go in `profileIds`. Vendor and Seller route to different backend services — pick the right one. See [Amazon Commerce Data Query scope](#amazon-commerce-data-query-scope) above for what's available per channel.
- **Walmart Commerce** → `commerce-walmart-vendor` (Vendor 1P only) → `materialType=vendor_account`, IDs go in `profileIds`. See [Walmart Commerce Data Query scope](#walmart-commerce-data-query-scope) above.

> Walmart commerce supports **Vendor (1P) only**, via Data Query (`commerce-walmart-vendor`). Walmart Seller (3P) commerce is not supported, and Walmart commerce is **not** available in Report MCP.

### SOV Query MCP (2 tools)

| Tool                | What it does                                                                                              |
| ------------------- | --------------------------------------------------------------------------------------------------------- |
| `list_sov_material` | Resolve SOV inputs — categories, brands, keyword tags (tree), keywords, zip codes (Walmart), stores.      |
| `query_sov`         | Run the SOV query — returns `brand` / `keyword` / `asin` tab as paginated JSON (`list`, `total`, paging). |

Canonical flow: `list_sov_material` → `query_sov`.

**Wired SOV platforms (11):** `amazon`, `walmart`, `instacart`, `target`, `kroger`, `criteo`, `citrus`, `bol`, `chewy`, `samsclub`, `doordash`.

**Required `query_sov` parameters:** `platform`, `tab` (`brand` | `keyword` | `asin`), `categoryIds`, `startDate`, `endDate`. Optional: `startCompare` / `endCompare`, `dim`, `deviceMode`, `brands`, `keywords`, `keywordTagIds`, `pageInfo`, `columns`. Metrics include `shareOfShelf`, `paidSOV`, `organicSOV`, `spSOV`, `sbSOV`, and tab-dependent top-N matrices.

**Platform-specific notes:**

- **Amazon & Walmart** — full `deviceMode` (`Aggregated` / `Separated` / `Mobile` / `PC` / `App`). Other nine platforms support `deviceMode=Aggregated` only.
- **Keyword tags** — `amazon`, `walmart`, `instacart`, `criteo` (brand & keyword tabs). Use **leaf** tag IDs in `query_sov`.
- **Walmart-only** — `zipCodes` filter via `materialType=zip_code`.
- `**instacart` / `criteo` / `citrus` / `doordash`\*\* — `retailerIds` via `materialType=store`.
- `**sov_group` / `sov_keyword_tag**` — name search isn't supported; pick IDs from the full list.

> **In-chat vs. file:** `query_sov` returns a paginated table inline — for spreadsheets / multi-tab ZIPs use Report MCP's `SOVReport` (the `ASIN-Keywords` tab requires `filters.sovBrands`, max 100 brands).

## Example prompts

**Report MCP — async export:**

```
Export last month's Walmart Campaigns where Spend > $500.
Columns: campaign name, impressions, clicks, spend, ACoS.
Send me the download link when it's ready.
```

```
Export last month's Amazon Campaigns to a CSV I can share.
```

**Data Query MCP — inline metrics in chat:**

```
Using Pacvue Data Query, show last 7 days Amazon campaign spend, ROAS, and impressions
by day for my main profile.
```

```
Using Pacvue Data Query, top 10 Amazon DSP line items by sales last month for advertiser X.
```

**SOV Query MCP — Share of Voice in chat:**

```
Using Pacvue SOV, show brand-level share of voice for my US beverage category last month —
break down paid vs organic.
```

```
Using Pacvue SOV, top 20 keywords by share of shelf for my Walmart juice category last week.
```

The agent will discover the schema at runtime, ask you to confirm any destructive parameters, and poll until the file is ready (Report MCP) or return rows directly in the thread (Data Query / SOV Query).

## Troubleshooting

### My browser opened to the Pacvue MCP gateway page

The gateway URL is an MCP endpoint, not a webpage. If it opened in your browser, your client tried to authenticate, failed, and fell back to the URL because no OAuth flow was available. Almost always this means the API token in `mcp.json` is missing, expired, or revoked.

Fix one of two ways and **fully restart your MCP client** afterwards (a window reload is not enough for some clients):

- **Use a token** — make sure `headers.Authorization` in `mcp.json` contains a current `pv_...` token (raw token, no `Bearer` prefix). Generate or rotate one under **Settings → MCP**.
- **Use OAuth instead** — remove the `headers` block from `mcp.json` entirely. The client will show a real **Connect** button that opens the Pacvue login page.

### Claude Desktop shows `pacvue-mcp` as failed / not connected (mcp-remote path)

If you went with the **Custom Connector** approach, you shouldn't hit any of these — connector errors usually surface as a clear message in the Connectors UI. The items below apply when you're using the `mcp-remote` bridge:

- `**npx` not found\*\* — Node.js isn't installed or isn't on `PATH`. Install Node LTS, restart Claude Desktop.
- **Wrong command on Windows** — use `npx.cmd`, not `npx`. Claude Desktop spawns the command directly without `cmd.exe`, so the extension matters.
- **Header has `Bearer` prefix** — the token must be raw (`Authorization: pv_...`), no `Bearer`.
- **Stale OAuth cache** — when switching between OAuth and API Token (or rotating tokens), clear `mcp-remote`'s cache: delete `~/.mcp-auth` (macOS/Linux) or `%USERPROFILE%\.mcp-auth` (Windows), then restart.

To see the underlying error, check Claude Desktop's MCP logs at `~/Library/Logs/Claude/mcp*.log` (macOS) or `%APPDATA%\Claude\logs\mcp*.log` (Windows).

If the bridge keeps misbehaving, the simplest fix is usually to remove the `mcpServers` entry from `claude_desktop_config.json` and switch to the Custom Connector path described above.

### Connection drops after a week of inactivity

Expected — OAuth refresh tokens have a 7-day sliding window. Reconnect through the browser flow and you're good for another 7 days. To eliminate the prompt entirely, switch to an API token.

### `run_report` failed with a missing-required-field error

The agent skipped a required filter or config. Required fields are enforced upstream by the platform's own API, not by the MCP layer. Re-run the request and tell the agent which platform / profile / time range you want — that's usually the missing piece.

### Data Query tools don't show up

Data Query MCP is available to any Pacvue user — there's no special entitlement to enable. If the tools don't appear, it's almost always a connection or auth issue: fully restart your MCP client and confirm your token / OAuth session is valid. The tool list is also scoped to the retailers your Console account can see, so you'll only get platforms you have access to.

### `execute_query` rejected with empty `profileIds` / `advertiserIds`

`execute_query` needs a non-empty scope. Resolve scope IDs first via `fetch_query_materials` (`materialType=profile` for standard ads & commerce; `materialType=advertiser` for Amazon DSP). Tell the agent the profile / advertiser name and let it look up the IDs.

### Agent mixed Report and Query calls in one task

Agents should pick one toolset per task — Report MCP **or** Data Query MCP **or** SOV Query MCP. If you see `run_report` showing up in the middle of a Data Query flow (or vice versa), restate the intent ("answer in chat" vs. "give me a file") and start a fresh turn.

## Security & limits

- All credentials (API tokens, OAuth access + refresh tokens) are stored hash-only on the server.
- Each credential is bound 1:1 to a Pacvue user. Data access is scoped to that user's Console entitlements.
- Issuance, use, and revocation are written to an audit log.
- Revocation is immediate — the next request with a revoked credential is rejected.
- Transport is TLS-only. Do not put `pv_...` tokens into chat history, screenshots, or shared configs.

| Hard cap                | Value                |
| ----------------------- | -------------------- |
| Report rows             | 150,000              |
| Query rows              | 500                  |
| Report download URL TTL | 24h                  |
| Report `taskId` TTL     | 24h                  |
| API tokens per user     | 50                   |
| API token max lifetime  | 180 days             |
| OAuth refresh token     | 7-day sliding window |

