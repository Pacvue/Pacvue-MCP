# Pacvue MCP

Generate Pacvue reports, answer questions in chat, and apply supported account changes from any MCP client. We focus on **Cursor, Claude (Code & Desktop), and ChatGPT**, and support any other agent that speaks the [Model Context Protocol](https://modelcontextprotocol.io).

One MCP server, no per-tool wiring. The agent only sees the toolsets your Pacvue account is entitled to.

> **Enterprise-managed clients (Claude, ChatGPT, and similar):** where connectors are governed at the workspace level, individual users may not be permitted to add Pacvue MCP as a Custom Connector. Please contact your IT administrator to have it provisioned for your organization.

## What you get

Tools are grouped into services. **GA** services are generally available. **Beta** services are available, with narrower platform coverage.

| Category | Service | Release | What it's for | Tools |
| --- | --- | --- | --- | --- |
| Data | Report MCP | GA | Download a CSV (or a ZIP of CSVs) for a report | `fetch_report_list`, `fetch_report_schema`, `fetch_report_materials`, `run_report`, `fetch_report_result` |
| Data | Data Query MCP | GA | Ad-hoc metrics in chat | `list_query_platforms`, `fetch_query_list`, `fetch_query_schema`, `fetch_query_materials`, `fetch_query_material_schema`, `execute_query` |
| Data | Brand Analytics | GA | Amazon and Walmart category search terms | `fetch_brand_analytics_search_terms` |
| Data | SOV Query MCP | GA | Share of Voice in chat | `list_sov_material`, `query_sov` |
| Data | Budget Manager MCP | GA | Monthly budget vs. spend from Budget Manager | `list_budget_platforms`, `fetch_budget_vs_spend` |
| Data | Event MCP | GA | Advertising change log and task history | `list_event_capabilities`, `fetch_event_schema`, `query_events` |
| Data | Creative Hub MCP | GA | Creative assets and their performance | `list_creative_hub_filter_options`, `fetch_creative_hub_materials`, `execute_creative_hub_asset_search`, `execute_creative_hub_asset_performance`, `search_creative_hub_asset_library` |
| Data | Global Report MCP | GA | Cross-retailer totals from Pacvue HQ Global Report | `fetch_global_report_options`, `query_global_report` |
| Data | AMC MCP | GA | Create an AMC report or audience directly in Amazon | `list_amc_instances`, `list_amc_advertisers`, `generate_amc_sql`, `generate_amc_report`, `generate_amc_audience`, `get_amc_job` |
| Data | Market Intelligence MCP | GA | Market share from Helium 10 Market 360 | `list_user_markets`, `get_market_statistics`, `get_market_trends`, `get_market_leaderboard`, `get_market_products` |
| Action | Campaign Management MCP | GA | Create or edit campaign settings. Not every platform or campaign type is supported | `list_campaign_mgmt_platforms`, `fetch_campaign_mgmt_schema`, `fetch_campaign_mgmt_materials`, `execute_campaign_mgmt_actions` |
| Action | Rule MCP | Beta | Create or edit automation rules | `list_rule_capabilities`, `get_rule_type_config`, `list_rules`, `list_rule_drafts`, `open_rule_draft`, `get_rule_draft`, `update_rule_draft`, `commit_rule_draft` |
| Action | SOV Management MCP | GA | Create, update, or delete a keyword SOV category | `manage_sov_category` |
| Account | Authorization MCP | GA | List the Pacvue clients this credential can act on | `fetch_authorized_clients` |

Report MCP is asynchronous: up to 150,000 rows, and the download link is valid for 24 hours. Data Query, SOV Query, Budget, Event, Creative Hub, Global Report, and Market Intelligence return JSON in the chat. Campaign, Rule, SOV Management, and AMC writes must be confirmed with you before the agent submits them.

New retailers and fields are picked up at runtime. The matrices below are the current footprint, not a frozen catalog.

## Choosing the right toolset

Use one toolset per task. Do not mix a file export with an in-chat query, or a read with a write, in the same job.

| User intent | Toolset |
| --- | --- |
| Download a CSV or ZIP | Report MCP |
| Show metrics in chat | Data Query MCP |
| Category search terms (Amazon, Walmart) | Brand Analytics |
| Share of Voice table in chat | SOV Query MCP |
| Bulk SOV export to a file | Report MCP (`SOV` report) |
| Monthly budget, pacing, utilization | Budget Manager MCP |
| What changed in an account | Event MCP |
| Creative assets or creative performance | Creative Hub MCP |
| One number across retailers | Global Report MCP |
| Amazon AMC report or audience | AMC MCP |
| Market share, trends, leaderboard, products | Market Intelligence MCP |
| Create or edit campaigns, bids, budgets, targets | Campaign Management MCP |
| Create or edit a rule | Rule MCP |
| Add, change, or remove an SOV category | SOV Management MCP |
| Which client should this run against | Authorization MCP |

## Retailer support

✓ means the service is available on that retailer. A blank cell means it is not available. You still only see retailers your Console account can access.

ChatGPT, TikTok, Reddit, and Pinterest currently expose **materials** in Data Query (accounts, campaigns, and related objects), not the full performance query used on the retail-media platforms.

### Ads

| Retailer | Report | Data Query | Brand Analytics | SOV | Budget | Event | Creative Hub | Global Report | Market Intelligence | Campaign Mgmt | Rule | AMC | SOV Mgmt |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Amazon Ads | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Amazon DSP | ✓ | ✓ |  |  | ✓ |  | ✓ | ✓ |  | ✓ |  | ✓ |  |
| Walmart | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  | ✓ | ✓ |  | ✓ |
| Target | ✓ | ✓ |  | ✓ | ✓ | ✓ |  | ✓ |  | ✓ | ✓ |  | ✓ |
| Instacart | ✓ | ✓ |  | ✓ | ✓ |  | ✓ | ✓ |  | ✓ |  |  | ✓ |
| Kroger | ✓ | ✓ |  | ✓ | ✓ |  |  | ✓ |  | ✓ |  |  | ✓ |
| Criteo | ✓ | ✓ |  | ✓ | ✓ |  |  | ✓ |  | ✓ |  |  | ✓ |
| Citrus | ✓ | ✓ |  | ✓ | ✓ |  |  | ✓ |  | ✓ |  |  | ✓ |
| Bol | ✓ | ✓ |  | ✓ | ✓ |  |  | ✓ |  | ✓ |  |  | ✓ |
| Chewy | ✓ | ✓ |  | ✓ | ✓ |  |  |  |  | ✓ |  |  | ✓ |
| DoorDash | ✓ | ✓ |  | ✓ | ✓ |  |  | ✓ |  | ✓ |  |  | ✓ |
| Sam's Club | ✓ | ✓ |  | ✓ | ✓ |  |  | ✓ |  | ✓ |  |  | ✓ |
| Kevel |  |  |  | ✓ |  |  |  |  |  |  |  |  | ✓ |
| Walmart DSP |  |  |  |  |  |  |  |  |  | ✓ |  |  |  |
| Roku |  |  |  |  |  |  |  |  |  | ✓ |  |  |  |
| Pacvue Direct |  |  |  |  |  |  |  |  |  | ✓ |  |  |  |
| Google |  |  |  |  |  |  |  |  |  | ✓ |  |  |  |
| Meta |  |  |  |  |  |  |  |  |  | ✓ |  |  |  |
| TikTok |  | ✓ |  |  |  |  | ✓ |  |  |  |  |  |  |
| ChatGPT |  | ✓ |  |  |  |  |  |  |  |  |  |  |  |
| Pinterest |  | ✓ |  |  |  |  |  |  |  |  |  |  |  |
| Reddit |  | ✓ |  |  |  |  |  |  |  |  |  |  |  |

Walmart is not currently available in Global Report.

### Commerce

| Retailer | Report | Data Query | Global Report |
| --- | --- | --- | --- |
| Amazon Commerce — Vendor (1P) | ✓ | ✓ | ✓ |
| Amazon Commerce — Seller (3P) | ✓ | ✓ | ✓ |
| Walmart Commerce — Vendor (1P) | ✓ | ✓ |  |
| Target Commerce — Vendor (1P) |  | ✓ |  |

Seller (3P) commerce for Walmart and Target is not available. Target Commerce is not available in Report MCP.

### Amazon Commerce Data Query scope

Vendor and Seller route to different platform keys: `commerce-amazon-vendor` and `commerce-amazon-seller`.

| Category | Vendor (1P) | Seller (3P) |
| --- | --- | --- |
| Sales | ✓ (Manufacturing + Sourcing view) | ✓ |
| Ads | ✓ | ✓ |
| Profitability (margin / COGS) | ✓ | ✓ |
| Inventory | ✓ (Manufacturing + Sourcing view) | ✓ |
| FBA inventory detail |  | ✓ |
| Content score | ✓ | ✓ |
| Buy Box / pricing | ✓ | ✓ |
| Promotion | ✓ | ✓ |
| Coupon | ✓ | ✓ |
| Subscribe & Save (SnS) | ✓ | ✓ |
| Alerts | ✓ | ✓ |
| PO (purchase orders) | ✓ |  |
| SOA tracker | ✓ |  |
| Order (order line items) |  | ✓ |
| Competitive Tracker | ✓ | ✓ |
| Forecast | ✓ | ✓ |
| Customer Feedback | ✓ | ✓ |
| Search Insight | ✓ | ✓ |
| BSR ranking | ✓ | ✓ |
| Real-time | ✓ | ✓ (hourly granularity) |

Vendor Sales and Inventory default to the Manufacturing view. Switch Sales to Sourcing via `options.reportType` (for example `sourcing_retail`). Inventory exposes both views as separate measure sets. Both are discoverable through `fetch_query_schema`.

### Walmart Commerce Data Query scope

Walmart Commerce is Vendor (1P) only, under `commerce-walmart-vendor`.

| Category | Vendor (1P) |
| --- | --- |
| Sales | ✓ |
| Inventory | ✓ |
| Buy Box / pricing | ✓ |

Resolve scope via `materialType=vendor_account`, then pass the IDs in `execute_query.profileIds`.

### Target Commerce Data Query scope

Target Commerce is Vendor (1P) only, under `commerce-target`.

| Category | Vendor (1P) |
| --- | --- |
| Sales | ✓ |
| RMS (Roundel ads) | ✓ |
| Inventory | ✓ |
| PO (purchase orders) | ✓ |

Scope is your Pacvue client and is applied from the credential. No `fetch_query_materials` call and no `profileIds` are required.

## Report coverage

Call `fetch_report_list` for the live catalog. These are the report levels currently wired.

### Amazon Ads

Profile, Campaign Tag, Campaign, Ad Group, Placement, Product Ads, Targeting, Query, Purchased Product, Product Eligibility, ASIN, SB Category Benchmark, SB Ads, Amazon Attribution, Portfolio, SOV.

### Amazon DSP

Campaign, Inventory, Audience, Product, Tag.

### Walmart

Profile, Campaign Tag, Campaign, Ad Group, Creative, Keyword, Tactic, Item, Item Health, Item Advanced Insight, Page Type, Placement, Platform, Query, Impression Share, Purchased Item, SOV, Hourly, Campaign Out of Daily Budget, Sales Lift, New Buyer, Category Intelligence.

### Other ads retailers

| Retailer | Report levels |
| --- | --- |
| Instacart | Profile, Campaign Tag, Campaign, Ad Group, Product Ads, Keyword, SOV, Promotion Group, Promotion, Promotion Product, Share of Shelf |
| Criteo | Profile, Campaign Tag, Campaign, Line Item, Product, Keyword, Page Type, Platform, SOV, Attributed Transaction, Hourly |
| Target | Profile, Campaign Tag, Campaign, Line Item, Product, Keyword, Page Type, Platform, SOV, Attributed Transaction, Hourly |
| Kroger | Profile, Campaign Tag, Campaign, Ad Group, Product, SOV, Targeting, Campaign Hourly |
| Chewy | Profile, Campaign Tag, Campaign, Product, Targeting, SOV, Purchased Product |
| Citrus | Team, Campaign Tag, Campaign, Placement, Keyword, Product, SOV, Advanced, Hourly |
| DoorDash | Profile, Campaign Tag, Campaign, Ad Group, Product Ads, Keyword, Product, Placement |
| Sam's Club | Profile, Campaign Tag, Campaign, Ad Group, Keyword, Product Ads, Page Type, Placement, Platform, Query, SOV, Campaign Hourly, Campaign Out of Budget |
| Bol | Profile, Campaign Tag, Campaign, Ad Group, Targeting, Product Ads, Query, SOV, Product |

### Commerce

| Retailer | Reports |
| --- | --- |
| Amazon Commerce | Sales & Inventory (vendor), Sales & Inventory (seller), Supply Chain (vendor), Profitability (vendor), Profitability (seller), Price Tracker, Consumption Forecast (vendor), Buy Box Tracker (vendor), Buy Box Tracker (seller) |
| Walmart Commerce | Sales (vendor), Inventory (vendor), Sales & Inventory (Marketplace) |

## Endpoint

```
https://mcp.pacvue.com/mcp
```

Every service above is exposed on this one URL. You do not configure toolsets individually.

## Authentication

Pacvue MCP reuses Pacvue's existing OAuth 2.0 system. Both options below resolve to a real Pacvue user, and data access is scoped to that user's Console entitlements.

You'll need a Pacvue Console account, and one of:

- An **API Token**, for clients that don't support browser OAuth, or
- An MCP client that supports OAuth 2.0 Authorization Code Flow with PKCE.

### Option A — API Token

1. Sign in to the Pacvue Console.
2. Go to **Settings → MCP → Create API Token**.
3. Give it a name, pick an expiry (max 180 days), and click **Create**.
4. **Copy the token immediately.** The full value (`pv_...`) is shown only once.
5. Paste it into the MCP client as the `Authorization` header. No `Bearer` prefix. Use the raw token.

To rotate or revoke a token, delete it on the same page. Up to 50 active tokens per user. Tokens are stored hash-only. Pacvue cannot recover a lost token.

### Option B — OAuth 2.0

For Cursor, Claude Code, Claude Desktop, ChatGPT, and other clients that support Dynamic Client Registration.

1. Add the server URL with no `headers` block.
2. On first connection the client opens a browser. Sign in to Pacvue and click **Authorize**.
3. The client stores an access token and a refresh token.
4. The refresh token has a **7-day sliding window**. Every refresh resets the TTL. After 7 days of no use, you sign in again.

To revoke a session, open **Settings → Connected Apps** in the Pacvue Console and remove the client. Each device has its own entry.

### More than one client

If the credential can access more than one Pacvue client, the agent calls `fetch_authorized_clients` and passes `clientId` on later calls. Omitting `clientId` uses the client bound to the credential.

## Client setup

The `Authorization` header takes the **raw token**. Do not add a `Bearer` prefix.

### Cursor

Add to `.cursor/mcp.json` (project) or `~/.cursor/mcp.json` (global).

OAuth:

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

### Claude Desktop and Claude (claude.ai)

**Recommended: Custom Connector (OAuth)**

1. Open **Settings → Connectors → Add custom connector**.
   - Claude Desktop: your name in the lower-left → **Settings** → **Connectors**.
   - Claude on the web: profile menu → **Settings** → **Connectors**.
2. Name: `Pacvue MCP`. Remote MCP server URL: `https://mcp.pacvue.com/mcp`.
3. Click **Add**, then **Connect**. Sign in and click **Authorize**.
4. The connector shows **Connected**. Entitled tools are available without a restart.

OAuth connections drop after 7 days of inactivity. To revoke, remove the connector, or remove the device under **Settings → Connected Apps** in the Pacvue Console.

The `mcp-remote` bridge below is only for API Token auth, or for a Claude Desktop build that does not show Connectors.

**API Token via `mcp-remote`**

Install [Node.js](https://nodejs.org/) LTS so `npx` is on your `PATH`.

Edit `claude_desktop_config.json`:

- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`

You can open it from **Claude → Settings → Developer → Edit Config**.

macOS / Linux:

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

Windows:

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

On Windows, use `npx.cmd`. Claude Desktop starts the command without a shell, so the `.cmd` extension is required.

Fully quit and relaunch Claude Desktop, including the tray icon. The `pacvue-mcp` entry should show as **running** under **Settings → Developer**.

### ChatGPT

ChatGPT uses OAuth only. It does not accept custom HTTP headers, so an API Token is not an option.

1. Open **Settings → Connectors → Add custom connector**.
2. Server URL: `https://mcp.pacvue.com/mcp`.
3. Sign in when the browser opens.

### Other MCP clients

Pacvue MCP is a streamable-HTTP MCP server. VS Code GitHub Copilot, Windsurf, Cline, and other compliant clients use the same two patterns:

- **OAuth** — point the client at `https://mcp.pacvue.com/mcp` with no headers.
- **API Token** — same URL, plus an `Authorization` header set to `pv_<your-api-token>`.

## Verify

Fully quit and restart the MCP client when the client requires it. The `pacvue-mcp` entry should show **Connected**.

Ask:

> What Pacvue tools do you have access to?

You should see the services in [What you get](#what-you-get) that your account is entitled to. A missing service usually means the account does not have that product, not a bad config. Rule MCP is Beta.

## Tool reference

You don't call these directly. They are listed so you can check the agent's plan.

### Report MCP

| Tool | What it does |
| --- | --- |
| `fetch_report_list` | List report types available to you, grouped by platform. |
| `fetch_report_schema` | Parameter schema, filters, requirements, and columns for one report. |
| `fetch_report_materials` | Resolve profiles, campaigns, tags, ASINs, and other filters to IDs. |
| `run_report` | Submit an async job and return a `taskId`. The agent must show a readable summary and wait for your confirmation. |
| `fetch_report_result` | Poll a `taskId`. Status is `PENDING`, `RUNNING`, `COMPLETED` (download URL), or `FAILED`. |

Flow: `fetch_report_list` → `fetch_report_schema` → `fetch_report_materials` when filters need IDs → `run_report` → `fetch_report_result`.

### Data Query MCP

| Tool | What it does |
| --- | --- |
| `list_query_platforms` | List query platforms available to you. No arguments. |
| `fetch_query_list` | Level hierarchy and attribute tables for one platform. |
| `fetch_query_schema` | Dimensions, measures, filter operators, and granularities for a platform and level. |
| `fetch_query_materials` | Resolve names to IDs. |
| `fetch_query_material_schema` | Schema for a material lookup, when the agent needs it. |
| `execute_query` | Run the query and return columns and rows in the chat. |

Flow: `list_query_platforms` → `fetch_query_list` → `fetch_query_schema` → `fetch_query_materials` for scope IDs → `execute_query`.

`execute_query` requires a non-empty scope, except Target Commerce:

- Standard ads → `materialType=profile` → `profileIds`
- Amazon DSP → `materialType=advertiser` → `advertiserIds`
- Amazon Commerce → `vendor_account` (1P) or `seller_account` (3P) → `profileIds`
- Walmart Commerce → `commerce-walmart-vendor` → `vendor_account` → `profileIds`
- Target Commerce → `commerce-target` → scope comes from the credential

Wired platform keys include `amazon-ads`, `amazon-dsp`, `walmart`, `instacart`, `target`, `kroger`, `criteo`, `citrus`, `bol`, `chewy`, `samsclub`, `doordash`, `commerce-amazon-vendor`, `commerce-amazon-seller`, `commerce-walmart-vendor`, and `commerce-target`. ChatGPT, TikTok, Reddit, and Pinterest are materials-only today.

### Brand Analytics

`fetch_brand_analytics_search_terms` returns Amazon and Walmart category search terms. It does not follow the Data Query flow.

### SOV Query MCP

| Tool | What it does |
| --- | --- |
| `list_sov_material` | Resolve categories, brands, keyword tags, keywords, Walmart zip codes, and stores. |
| `query_sov` | Return a `brand`, `keyword`, or `asin` tab as paginated JSON. |

Flow: `list_sov_material` → `query_sov`.

Required on `query_sov`: `platform`, `tab` (`brand`, `keyword`, or `asin`), `categoryIds`, `startDate`, `endDate`.

Amazon and Walmart support `deviceMode` values `Aggregated`, `Separated`, `Mobile`, `PC`, and `App`. The other SOV platforms support `Aggregated` only. Keyword tag filters work on Amazon, Walmart, Instacart, and Criteo. Walmart has a `zip_code` filter. Instacart, Criteo, Citrus, and DoorDash use `store` (`retailerIds`).

For a spreadsheet or a multi-tab ZIP, use Report MCP's SOV report instead of `query_sov`.

### Budget Manager MCP

| Tool | What it does |
| --- | --- |
| `list_budget_platforms` | Platforms, profile labels, and budget-line sub-types. |
| `fetch_budget_vs_spend` | Monthly budget, spend, pacing, and utilization by profile, and by tag where the platform uses tags. |

This is Budget Manager data, not an ad-performance report. Use Report MCP or Data Query MCP for campaign metrics.

Platforms: Amazon, Walmart, Amazon DSP, Instacart, Criteo, Target, Kroger, Sam's Club, Chewy, Citrus, DoorDash, Bol.

Do not add spend across rows that use different currencies. The tool does not return FX rates.

### Event MCP

| Tool | What it does |
| --- | --- |
| `list_event_capabilities` | Platforms and views you can query. |
| `fetch_event_schema` | Tabs and allowed filter values for one view. |
| `query_events` | Change-log or task-center rows for one platform and view. |

The maximum date range is 90 days. Filter values must come from `fetch_event_schema`. `profileIds` is required.

### Creative Hub MCP

| Tool | What it does |
| --- | --- |
| `list_creative_hub_filter_options` | File types, ad types, and tag filters. |
| `fetch_creative_hub_materials` | Resolve profile, campaign, ad group, creative, and campaign-tag IDs. |
| `execute_creative_hub_asset_search` | Asset metadata. No performance metrics. |
| `execute_creative_hub_asset_performance` | Impressions, clicks, spend, ROAS, and related metrics for Image and Video assets. |
| `search_creative_hub_asset_library` | How many assets exist, including assets that have not run. |

Performance only includes assets that delivered inside the date range. Logos do not have their own performance. Use `search_creative_hub_asset_library` for inventory counts.

### Global Report MCP

| Tool | What it does |
| --- | --- |
| `fetch_global_report_options` | Metrics, dimensions, and filter candidates. |
| `query_global_report` | Cross-retailer totals, a breakdown, or a time series. |

Stay inside this toolset for Global Report questions. A single-retailer query cannot reproduce the cross-retailer total.

- Omit dimensions for one portfolio total.
- Pass dimensions for a breakdown. `REGION`, `COUNTRY`, `RETAILER`, `CLIENT`, and `PROFILE` combine. Commerce metrics break down by `REGION` only.
- Pass `timeGranularity` (`DAY`, `WEEK`, `MONTH`, `QUARTER`, `YEAR`) for a time series. Weeks are Sunday–Saturday.
- `null` means no data. It does not mean zero.
- Percent metrics are decimal fractions (`0.3544` is 35.44%). ROAS is a multiple, not a percent.
- Money is converted to `meta.currency`.

### AMC MCP

This service submits work to Amazon AMC. It does not create a Pacvue-side AMC object.

| Tool | What it does |
| --- | --- |
| `list_amc_instances` | AMC instances you can use. |
| `list_amc_advertisers` | Advertisers on the instance you chose, including Display vs. Sponsored Ads. |
| `generate_amc_sql` | Turn an English question into AMC SQL. Does not run the query. |
| `generate_amc_report` | Submit confirmed analytical SQL. Returns `externalJobId`. |
| `generate_amc_audience` | Submit a confirmed audience query. Returns `externalJobId`. |
| `get_amc_job` | Poll a job by `externalJobId`. |

Flow: `list_amc_instances` → you pick an instance → `list_amc_advertisers` when building an audience → `generate_amc_sql` → you confirm the SQL → `generate_amc_report` or `generate_amc_audience` → `get_amc_job`.

The question sent to `generate_amc_sql` must be English. Do not invent the time window. SQL generation often takes 40–180 seconds.

### Market Intelligence MCP

| Tool | What it does |
| --- | --- |
| `list_user_markets` | Markets on the account. Call this first. |
| `get_market_statistics` | Headline share, revenue, and units for one market. |
| `get_market_trends` | Share and sales over time. |
| `get_market_leaderboard` | Brand ranking in a market. |
| `get_market_products` | Product rows in a market. |

Share and change fields are already in percentage points. Do not divide them by 100.

### Campaign Management MCP

The agent must show the exact changes and wait for your confirmation before `execute_campaign_mgmt_actions`.

| Tool | What it does |
| --- | --- |
| `list_campaign_mgmt_platforms` | Platforms and action types you can run. |
| `fetch_campaign_mgmt_schema` | Request body for one action. |
| `fetch_campaign_mgmt_materials` | Resolve profiles and parent entities to IDs. |
| `execute_campaign_mgmt_actions` | Create, update, or delete. Results return in the response. |

Supported retailers are in the [ads matrix](#ads). Not every campaign type on those retailers can be edited. The schema call is the source of truth for fields. On Amazon, one write request can contain only one ad product (Sponsored Products, Sponsored Brands, Sponsored Display, or Sponsored TV). Split mixed updates into separate actions.

### Rule MCP

Beta. Creating or changing a rule goes through a draft. The agent must preview the draft and wait for your confirmation before `commit_rule_draft`.

| Tool | What it does |
| --- | --- |
| `list_rule_capabilities` | Platforms and rule types you can use. |
| `get_rule_type_config` | Fields and constraints for one rule type. |
| `list_rules` | Existing rules. |
| `list_rule_drafts` | Open drafts. |
| `open_rule_draft` | Start a create or edit draft. |
| `get_rule_draft` | Read one draft. |
| `update_rule_draft` | Change the draft, save it, or preview it. |
| `commit_rule_draft` | Apply a confirmed draft. |

Rule MCP is available on Amazon Ads, Walmart, and Target. It is not available on Amazon DSP. Call `list_rule_capabilities` for the objects a platform actually supports (campaign, targeting, placement, ad group, product, keyword, and so on).

### SOV Management MCP

`manage_sov_category` creates, updates, or deletes a keyword SOV category. The agent must read the current category with `list_sov_material`, restate the change, and wait for your confirmation. Delete requires `confirm=true` after you approve that specific category.

One update call changes one field. There is no replace-the-whole-list operation. Add and remove are separate calls.

### Authorization MCP

`fetch_authorized_clients` lists the Pacvue clients this credential can act on. Pass the chosen `clientId` into later calls when you have more than one.

## Example prompts

**Report MCP**

```
Export last month's Walmart campaigns where spend was over $500.
Columns: campaign name, impressions, clicks, spend, ACoS.
Send me the download link when it's ready.
```

**Data Query MCP**

```
Using Pacvue Data Query, show the last 7 days of Amazon campaign spend, ROAS, and impressions by day for my main profile.
```

**Budget Manager**

```
Using Pacvue Budget Manager, show this month's Amazon budget vs. spend by profile. Do not combine currencies.
```

**Global Report**

```
Using Pacvue Global Report, give me last month's total ad spend and ROAS across retailers, then break it down by retailer.
```

**Campaign Management**

```
Pause the Amazon Sponsored Products campaign "Brand - Exact" on my US profile.
Show me the exact change and wait for my confirmation before submitting.
```

**AMC**

```
On my AMC instance, draft SQL for shoppers who viewed but did not purchase in the last 30 days.
Show me the SQL and wait before you submit a report.
```

The agent discovers the schema at runtime. For a file export it polls until the link is ready. For a write it stops and asks you to confirm.

## Troubleshooting

### The browser opened the Pacvue MCP gateway page

The gateway URL is an MCP endpoint, not a webpage. This usually means the API token is missing, expired, or revoked.

Fully restart the client, then either:

- Put a current `pv_...` token in `headers.Authorization` with no `Bearer` prefix, or
- Remove the `headers` block and use OAuth.

### Claude Desktop shows `pacvue-mcp` as failed (`mcp-remote`)

Custom Connector errors show up in the Connectors UI. The items below apply to the `mcp-remote` bridge:

- **`npx` not found** — install Node.js LTS and restart Claude Desktop.
- **Windows command** — use `npx.cmd`, not `npx`.
- **`Bearer` prefix** — the header value must be the raw `pv_...` token.
- **Stale OAuth cache** — when switching between OAuth and a token, delete `~/.mcp-auth` (macOS/Linux) or `%USERPROFILE%\.mcp-auth` (Windows), then restart.

Logs: `~/Library/Logs/Claude/mcp*.log` on macOS, `%APPDATA%\Claude\logs\mcp*.log` on Windows.

### Connection drops after a week of inactivity

OAuth refresh tokens use a 7-day sliding window. Sign in again, or switch to an API token.

### `run_report` failed on a missing field

A required filter or config was skipped. Tell the agent the platform, profile, and date range, and let it re-read `fetch_report_schema`.

### A toolset is missing

Restart the client and confirm the token or OAuth session. The tool list is also limited to products and retailers on your Console account. Rule MCP is Beta and can be absent when the account is not in that beta.

### `execute_query` rejected an empty scope

Resolve IDs with `fetch_query_materials` first. Target Commerce is the exception: its scope comes from the credential.

### The agent mixed toolsets

Restate the intent — file, in-chat answer, or a specific write — and start a fresh turn. Writes still need an explicit confirmation even when the intent is clear.

## Security and limits

- API tokens and OAuth tokens are stored hash-only.
- Each credential maps to one Pacvue user. Data and writes follow that user's Console permissions.
- Issuance, use, and revocation are audit-logged.
- Revocation takes effect on the next request.
- Transport is TLS only. Do not put `pv_...` tokens in chat, screenshots, or shared config files.

| Limit | Value |
| --- | --- |
| Report rows | 150,000 |
| Query rows | 500 |
| Report download URL | 24 hours |
| Report `taskId` | 24 hours |
| Event MCP date range | 90 days |
| API tokens per user | 50 |
| API token lifetime | 180 days |
| OAuth refresh token | 7-day sliding window |
