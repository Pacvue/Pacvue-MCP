# Pacvue MCP

Generate Pacvue ad reports from any MCP client. We focus on **Cursor, Claude (Code & Desktop), and ChatGPT**, and support any other agent that speaks the [Model Context Protocol](https://modelcontextprotocol.io).

One MCP server, no per-tool wiring.

## What you get


| Capability         | What it's for                                                       | Output                                                        | Limits                                     |
| ------------------ | ------------------------------------------------------------------- | ------------------------------------------------------------- | ------------------------------------------ |
| **Report MCP**     | Ad-hoc one-off report exports — "give me a file I can save / share" | CSV (or ZIP of CSVs for multi-tab reports) via pre-signed URL | Async, ≤ 100,000 rows, 24h download window |
| **Data Query MCP** | *Coming soon.*                                                      | —                                                             | —                                          |


Report MCP covers all retailers your Pacvue account has access to. New retailers and new fields are picked up automatically — no MCP-side changes.

## Supported platforms & report types

Below is a snapshot of the report types currently exposed by Pacvue MCP, grouped by platform. The actual list returned to your agent is scoped to your Pacvue Console entitlements — you will only see platforms and reports you have access to.

> The agent retrieves this list at runtime via `fetch_report_list`; the tables below are for reference only. Internal `reportType` identifiers are shown in code spans.

### Amazon

**Amazon Ads** — 17 reports


| Report                       | `reportType`                | Description                                                   |
| ---------------------------- | --------------------------- | ------------------------------------------------------------- |
| Profile Report               | `ProfileReport`             | Performance summary at the profile (account) level            |
| Campaign Report              | `CampaignReport`            | Campaign-level metrics with daily/weekly/monthly breakdowns   |
| Ad Group Report              | `AdGroupReport`             | Ad group level performance data                               |
| Campaign Tag Report          | `CampaignTagReport`         | Performance aggregated by campaign tags (SB/SD)               |
| Targeting Report             | `TargetingReport`           | Targeting-level performance data                              |
| Search Term Report           | `QueryReport`               | Search query performance and matched keyword data             |
| Product Ads Report           | `ProductsADReport`          | Product-level advertising performance                         |
| Placement Report             | `PlacementReport`           | Ad placement performance (Top of Search, Product Pages, etc.) |
| Portfolio Report             | `PortfolioReport`           | Portfolio-level performance aggregation                       |
| ASIN Report                  | `ASINReport`                | ASIN-level performance data                                   |
| Purchased Product Report     | `PurchasedProductReport`    | Products purchased after ad click                             |
| Product Eligibility Report   | `ProductEligibilityReport`  | Product advertising eligibility status                        |
| SB Category Benchmark Report | `SBCategoryBenchmarkReport` | Sponsored Brands category benchmark data                      |
| SOV Report                   | `SOVReport`                 | Share of Voice metrics by keyword group and brand             |
| Attribution Report           | `AttributionReport`         | Amazon Attribution conversion data                            |
| SP Hourly Report             | `SPHourlyReport`            | Sponsored Products hourly performance                         |
| SB Ads Report                | `SBAdsReport`               | Sponsored Brands ad-level metrics                             |




**Amazon DSP** — 5 reports


| Report                      | `reportType`      | Description                                                                          |
| --------------------------- | ----------------- | ------------------------------------------------------------------------------------ |
| Campaign Report             | `CampaignReport`  | DSP campaign performance with advertiser/order/lineItem/creative breakdowns          |
| Inventory Report            | `InventoryReport` | DSP inventory performance with advertiser/order/lineItem/site/supply/deal breakdowns |
| Audience Report             | `AudienceReport`  | DSP audience performance with advertiser/order/lineItem breakdowns                   |
| Product (Conversion) Report | `ProductReport`   | DSP product/conversion performance with advertiser/order/lineItem breakdowns         |
| Tag Report                  | `TagReport`       | DSP tag performance with orderTag/lineItemTag/creativeTag breakdowns                 |




**Amazon Commerce** — 9 reports (1P Vendor + 3P Seller)


| Report                              | `reportType`                    | Channel     | Description                             |
| ----------------------------------- | ------------------------------- | ----------- | --------------------------------------- |
| Sales & Inventory Report For Vendor | `SalesInventoryReportForVendor` | Vendor (1P) | Vendor-mode sales and inventory metrics |
| Supply Chain Report For Vendor      | `SupplyChainReportForVendor`    | Vendor (1P) | Vendor supply chain analytics           |
| Profitability Report For Vendor     | `ProfitabilityReportForVendor`  | Vendor (1P) | Vendor profitability analysis           |
| Price Tracker Report                | `PriceTrackerReport`            | Vendor (1P) | Vendor-mode price tracking data         |
| Consumption Forecast Report         | `ConsumptionForecastReport`     | Vendor (1P) | Vendor demand forecasting               |
| Buybox Tracker Report For Vendor    | `BuyboxTrackerReportForVendor`  | Vendor (1P) | Vendor-mode buybox tracking data        |
| Sales & Inventory Report For Seller | `SalesInventoryReportForSeller` | Seller (3P) | Seller-mode sales and inventory metrics |
| Profitability Report For Seller     | `ProfitabilityReportForSeller`  | Seller (3P) | Seller-mode profitability analysis      |
| Buybox Tracker Report For Seller    | `BuyboxTrackerReportForSeller`  | Seller (3P) | Seller-mode buybox tracking data        |


> Commerce reports are routed by `channel`. Tell your agent whether you want 1P (Vendor) or 3P (Seller) — `*ForVendor` and `*ForSeller` go to different backend services.



### Walmart

**Walmart Ads** — 22 reports


| Report                        | `reportType`                     | Description                                |
| ----------------------------- | -------------------------------- | ------------------------------------------ |
| Profile Report                | `ProfileReport`                  | Profile-level summary for Walmart          |
| Campaign Report               | `CampaignReport`                 | Campaign-level performance for Walmart ads |
| Ad Group Report               | `AdGroupReport`                  | Ad group level metrics                     |
| Campaign Tag Report           | `TagReport`                      | Performance aggregated by campaign tags    |
| Keyword Report                | `KeywordReport`                  | Keyword-level performance                  |
| Search Term Report            | `QueryReport`                    | Search query performance                   |
| Creative Report               | `CreativeReport`                 | Creative-level performance data            |
| Tactic Report                 | `TacticReport`                   | Tactic-level performance data              |
| Page Type Report              | `PageTypeReport`                 | Performance by page type placement         |
| Platform Report               | `PlatformReport`                 | Cross-platform performance summary         |
| Placement Report              | `PlacementReport`                | Ad placement performance                   |
| Item Report                   | `ItemReport`                     | Item-level performance data                |
| Item Health Report            | `ItemHealthReport`               | Item listing health and performance        |
| Item Advanced Insight Report  | `ItemAdvancedInsightReport`      | Item-level advanced insight metrics        |
| Purchased Item Report         | `PurchasedItemReport`            | Items purchased after ad interaction       |
| SOV Report                    | `SOVReport`                      | Share of Voice metrics for Walmart         |
| Impression Share Report       | `ImpressionShareReport`          | Impression share and competitive metrics   |
| Sales Lift Report             | `SalesLiftReport`                | Incremental sales lift attributed to ads   |
| New Buyer Report              | `NewBuyerReport`                 | New buyer acquisition metrics              |
| Category Intelligence Report  | `CategoryIntelligenceReport`     | Category-level competitive intelligence    |
| Campaign Hourly Report        | `HourlyReport`                   | Hourly campaign performance                |
| Campaign Out of Budget Report | `CampaignOutOfDailyBudgetReport` | Campaigns running out of daily budget      |




**Commerce Walmart** — 3 reports


| Report                   | `reportType`           |
| ------------------------ | ---------------------- |
| Sales Report             | `SalesReport`          |
| Inventory Report         | `InventoryReport`      |
| Sales & Inventory Report | `SalesInventoryReport` |




### Other Retailers

**Instacart** — 16 reports


| Report                        | `reportType`                  | Description                                |
| ----------------------------- | ----------------------------- | ------------------------------------------ |
| Profile Report                | `ProfileReport`               | Profile-level performance summary          |
| Campaign Report               | `CampaignReport`              | Campaign-level performance metrics         |
| Adgroup Report                | `AdGroupReport`               | Adgroup-level performance data             |
| Campaign Tag Report           | `CampaignTagReport`           | Performance aggregated by campaign tags    |
| Keyword Report                | `KeywordReport`               | Keyword-level bidding and performance      |
| Product Ads Report            | `ProductAdsReport`            | Product-level advertising performance      |
| Product Report                | `ProductReport`               | Product-level performance data             |
| Page Type Report              | `PageTypeReport`              | Performance by page type placement         |
| Platform Report               | `PlatformReport`              | Cross-platform performance summary         |
| Hourly Report                 | `HourlyReport`                | Hourly campaign performance                |
| SOV Report                    | `SOVReport`                   | Share of Voice by keyword and brand        |
| Share of Shelf Report         | `ShareOfShelfReport`          | Share of Shelf metrics (requires SDS data) |
| Attributed Transaction Report | `AttributedTransactionReport` | Transactions attributed to ads             |
| Promotion Group Report        | `PromotionGroupReport`        | Promotion group dimension performance      |
| Promotion Report              | `PromotionReport`             | Promotion-level performance data           |
| Promotion Product Report      | `PromotionProductReport`      | Promotion product dimension performance    |




**Kroger** — 8 reports


| Report                 | `reportType`           |
| ---------------------- | ---------------------- |
| Profile Report         | `ProfileReport`        |
| Campaign Report        | `CampaignReport`       |
| Campaign Tag Report    | `CampaignTagReport`    |
| AdGroup Report         | `AdgroupReport`        |
| Targeting Report       | `TargetingReport`      |
| Product Report         | `ProductReport`        |
| SOV Report             | `SOVReport`            |
| Campaign Hourly Report | `CampaignHourlyReport` |




**DoorDash** — 8 reports


| Report              | `reportType`           |
| ------------------- | ---------------------- |
| Profile Report      | `ProfileReport`        |
| Campaign Report     | `CampaignReport`       |
| Campaign Tag Report | `CampaignTagReport`    |
| Adgroup Report      | `AdgroupReport`        |
| Keyword Report      | `KeywordReport`        |
| Product Ads Report  | `ProductReport`        |
| Product Report      | `ProfileProductReport` |
| Placement Report    | `PlacementReport`      |




**Sam's Club** — 13 reports


| Report                        | `reportType`                     |
| ----------------------------- | -------------------------------- |
| Profile Report                | `ProfileReport`                  |
| Campaign Report               | `CampaignReport`                 |
| Campaign Tag Report           | `CampaignTagReport`              |
| AdGroup Report                | `AdgroupReport`                  |
| Keyword Report                | `KeywordReport`                  |
| Product Ads Report            | `ProductReport`                  |
| Page Type Report              | `PageTypeReport`                 |
| Placement Report              | `PlacementReport`                |
| Platform Report               | `PlatformReport`                 |
| Query Report                  | `QueryReport`                    |
| SOV Report                    | `SOVReport`                      |
| Campaign Hourly Report        | `HourlyReport`                   |
| Campaign Out of Budget Report | `CampaignOutOfDailyBudgetReport` |






**Target** — 11 reports


| Report                        | `reportType`                  |
| ----------------------------- | ----------------------------- |
| Profile Report                | `ProfileReport`               |
| Campaign Report               | `CampaignReport`              |
| Campaign Tag Report           | `CampaignTagReport`           |
| Line Item Report              | `LineItemReport`              |
| Keyword Report                | `KeywordReport`               |
| Product Report                | `ProductReport`               |
| Page Type Report              | `PageTypeReport`              |
| Platform Report               | `PlatformReport`              |
| SOV Report                    | `SOVReport`                   |
| Attributed Transaction Report | `AttributedTransactionReport` |
| Hourly Report                 | `HourlyReport`                |




**Criteo** — 11 reports


| Report                        | `reportType`                  |
| ----------------------------- | ----------------------------- |
| Profile Report                | `ProfileReport`               |
| Campaign Report               | `CampaignReport`              |
| Campaign Tag Report           | `CampaignTagReport`           |
| Line Item Report              | `LineItemReport`              |
| Keyword Report                | `KeywordReport`               |
| Product Report                | `ProductReport`               |
| Page Type Report              | `PageTypeReport`              |
| Platform Report               | `PlatformReport`              |
| SOV Report                    | `SOVReport`                   |
| Attributed Transaction Report | `AttributedTransactionReport` |
| Hourly Report                 | `HourlyReport`                |




**Citrus** — 9 reports


| Report              | `reportType`        |
| ------------------- | ------------------- |
| Team Report         | `TeamReport`        |
| Campaign Report     | `CampaignReport`    |
| Campaign Tag Report | `CampaignTagReport` |
| Keyword Report      | `KeywordReport`     |
| Product Report      | `ProductReport`     |
| Placement Report    | `PlacementReport`   |
| SOV Report          | `SOVReport`         |
| Advanced Report     | `AdvancedReport`    |
| Hourly Report       | `HourlyReport`      |




**Chewy** — 7 reports


| Report                   | `reportType`             |
| ------------------------ | ------------------------ |
| Profile Report           | `ProfileReport`          |
| Campaign Report          | `CampaignReport`         |
| Campaign Tag Report      | `CampaignTagReport`      |
| Targeting Report         | `TargetingReport`        |
| Product Report           | `ProductReport`          |
| Purchased Product Report | `PurchasedProductReport` |
| SOV Report               | `SOVReport`              |




**Bol** — 9 reports


| Report              | `reportType`               |
| ------------------- | -------------------------- |
| Profile Report      | `ProfileReport`            |
| Campaign Report     | `CampaignReport`           |
| Campaign Tag Report | `CampaignTagReport`        |
| Adgroup Report      | `AdgroupReport`            |
| Targeting Report    | `TargetingReport`          |
| Product Ads Report  | `ProductReport`            |
| Product Report      | `ProductAttributionReport` |
| Query Report        | `QueryReport`              |
| SOV Report          | `SOVReport`                |




## Endpoint

```
https://mcp.pacvue.com/mcp
```

All 5 tools are exposed under one server entry. You do not configure them individually.

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

You should see 5 tools from Report MCP. (Data Query MCP is coming soon.)

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

### Data Query MCP

*Coming soon.*

## Example prompts

```
Export last month's Walmart Campaigns where Spend > $500.
Columns: campaign name, impressions, clicks, spend, ACoS.
Send me the download link when it's ready.
```

```
Export last month's Amazon Campaigns to a CSV I can share.
```

The agent will discover the schema at runtime, ask you to confirm any destructive parameters, and poll until the file is ready.

## Troubleshooting

### My browser opened to the Pacvue MCP gateway page

The gateway URL is an MCP endpoint, not a webpage. If it opened in your browser, your client tried to authenticate, failed, and fell back to the URL because no OAuth flow was available. Almost always this means the API token in `mcp.json` is missing, expired, or revoked.

Fix one of two ways and **fully restart your MCP client** afterwards (a window reload is not enough for some clients):

- **Use a token** — make sure `headers.Authorization` in `mcp.json` contains a current `pv_...` token (raw token, no `Bearer` prefix). Generate or rotate one under **Settings → MCP**.
- **Use OAuth instead** — remove the `headers` block from `mcp.json` entirely. The client will show a real **Connect** button that opens the Pacvue login page.

### Claude Desktop shows `pacvue-mcp` as failed / not connected (mcp-remote path)

If you went with the **Custom Connector** approach, you shouldn't hit any of these — connector errors usually surface as a clear message in the Connectors UI. The items below apply when you're using the `mcp-remote` bridge:

- `**npx` not found** — Node.js isn't installed or isn't on `PATH`. Install Node LTS, restart Claude Desktop.
- **Wrong command on Windows** — use `npx.cmd`, not `npx`. Claude Desktop spawns the command directly without `cmd.exe`, so the extension matters.
- **Header has `Bearer`  prefix** — the token must be raw (`Authorization: pv_...`), no `Bearer`.
- **Stale OAuth cache** — when switching between OAuth and API Token (or rotating tokens), clear `mcp-remote`'s cache: delete `~/.mcp-auth` (macOS/Linux) or `%USERPROFILE%\.mcp-auth` (Windows), then restart.

To see the underlying error, check Claude Desktop's MCP logs at `~/Library/Logs/Claude/mcp*.log` (macOS) or `%APPDATA%\Claude\logs\mcp*.log` (Windows).

If the bridge keeps misbehaving, the simplest fix is usually to remove the `mcpServers` entry from `claude_desktop_config.json` and switch to the Custom Connector path described above.

### Connection drops after a week of inactivity

Expected — OAuth refresh tokens have a 7-day sliding window. Reconnect through the browser flow and you're good for another 7 days. To eliminate the prompt entirely, switch to an API token.

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
| API tokens per user     | 50                   |
| API token max lifetime  | 180 days             |
| OAuth refresh token     | 7-day sliding window |


