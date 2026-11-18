# Pacvue MCP

Generate Pacvue reports, answer questions in chat, and apply supported account changes from any MCP client. We focus on **Cursor, Claude (Code & Desktop), and ChatGPT**, and support any other agent that speaks the [Model Context Protocol](https://modelcontextprotocol.io).

One MCP server, no per-tool wiring. The agent only sees the toolsets your Pacvue account is entitled to.

> **Enterprise-managed clients (Claude, ChatGPT, and similar):** where connectors are governed at the workspace level, individual users may not be permitted to add Pacvue MCP as a Custom Connector. Please contact your IT administrator to have it provisioned for your organization.

## Contents

- [What you get](#what-you-get)
- [Choosing the right toolset](#choosing-the-right-toolset)
- [Endpoint](#endpoint)
- [Authentication](#authentication)
  - [Option A — API Token](#option-a--api-token)
    - [Renew an expired token](#renew-an-expired-token)
  - [Option B — OAuth 2.0](#option-b--oauth-20)
  - [More than one client](#more-than-one-client)
- [Client setup](#client-setup)
  - [Cursor](#cursor)
  - [Claude Code](#claude-code)
  - [Claude Desktop and Claude (claude.ai)](#claude-desktop-and-claude-claudeai)
  - [ChatGPT](#chatgpt)
  - [Other MCP clients](#other-mcp-clients)
- [Verify](#verify)
- [Tool reference](#tool-reference)
  - [Report MCP](#report-mcp)
  - [Data Query MCP](#data-query-mcp)
  - [Brand Analytics](#brand-analytics)
  - [SOV Query MCP](#sov-query-mcp)
  - [Budget Manager MCP](#budget-manager-mcp)
  - [Event MCP](#event-mcp)
  - [Creative Hub MCP](#creative-hub-mcp)
  - [Global Report MCP](#global-report-mcp)
  - [AMC MCP](#amc-mcp)
  - [Market Intelligence MCP](#market-intelligence-mcp)
  - [Campaign Management MCP](#campaign-management-mcp)
  - [Tag Management MCP](#tag-management-mcp)
  - [SOV Management MCP](#sov-management-mcp)
  - [Authorization MCP](#authorization-mcp)
- [Example prompts](#example-prompts)
- [Troubleshooting](#troubleshooting)
  - [The browser opened the Pacvue MCP gateway page](#the-browser-opened-the-pacvue-mcp-gateway-page)
  - [API token expired](#api-token-expired)
  - [Claude Desktop shows `pacvue-mcp` as failed](#claude-desktop-shows-pacvue-mcp-as-failed-mcp-remote)
  - [Connection drops after a week of inactivity](#connection-drops-after-a-week-of-inactivity)
  - [`run_report` failed on a missing field](#run_report-failed-on-a-missing-field)
  - [A toolset is missing](#a-toolset-is-missing)
  - [`execute_query` rejected an empty scope](#execute_query-rejected-an-empty-scope)
  - [The agent mixed toolsets](#the-agent-mixed-toolsets)
- [Security and limits](#security-and-limits)

## What you get

| Category | Service | What it's for | Tools |
| --- | --- | --- | --- |
| Data | Report MCP | Download a CSV (or a ZIP of CSVs) for a report | `fetch_report_list`, `fetch_report_schema`, `fetch_report_materials`, `run_report`, `fetch_report_result` |
| Data | Data Query MCP | Ad-hoc metrics in chat | `list_query_platforms`, `fetch_query_list`, `fetch_query_schema`, `fetch_query_materials`, `fetch_query_material_schema`, `execute_query` |
| Data | Brand Analytics | Amazon and Walmart category search terms | `fetch_brand_analytics_search_terms` |
| Data | SOV Query MCP | Share of Voice in chat | `list_sov_material`, `query_sov` |
| Data | Budget Manager MCP | Monthly budget vs. spend from Budget Manager | `list_budget_platforms`, `fetch_budget_vs_spend` |
| Data | Event MCP | Advertising change log and task history | `list_event_capabilities`, `fetch_event_schema`, `query_events` |
| Data | Creative Hub MCP | Creative assets and their performance | `list_creative_hub_filter_options`, `fetch_creative_hub_materials`, `execute_creative_hub_asset_search`, `execute_creative_hub_asset_performance`, `search_creative_hub_asset_library` |
| Data | Global Report MCP | Cross-retailer totals from Pacvue HQ Global Report | `fetch_global_report_options`, `query_global_report` |
| Data | AMC MCP | Create an AMC report or audience directly in Amazon | `list_amc_instances`, `list_amc_advertisers`, `generate_amc_sql`, `generate_amc_report`, `generate_amc_audience`, `get_amc_job` |
| Data | Market Intelligence MCP | Market share from Helium 10 Market 360 | `list_user_markets`, `get_market_statistics`, `get_market_trends`, `get_market_leaderboard`, `get_market_products` |
| Action | Campaign Management MCP | Create or edit campaign settings | `list_campaign_mgmt_platforms`, `fetch_campaign_mgmt_schema`, `fetch_campaign_mgmt_materials`, `execute_campaign_mgmt_actions` |
| Action | Tag Management MCP | Create and edit tags, and add or remove the items on a tag | `list_tag_capabilities`, `list_tags`, `query_tag_items`, `manage_tag`, `delete_tag`, `preview_tag_item_change`, `apply_tag_item_change` |
| Action | SOV Management MCP | Create, update, or delete a keyword SOV category | `manage_sov_category` |
| Account | Authorization MCP | List the Pacvue clients this credential can act on | `fetch_authorized_clients` |

Report MCP is asynchronous: up to 150,000 rows, and the download link is valid for 24 hours. Data Query, SOV Query, Budget, Event, Creative Hub, Global Report, and Market Intelligence return JSON in the chat. Campaign, Tag Management, SOV Management, and AMC writes must be confirmed with you before the agent submits them.

Which retailers, reports, and fields you can use is decided at runtime from your Console entitlements. There is no static catalog in this document.

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
| Create a tag, or add and remove items on a tag | Tag Management MCP |
| Add, change, or remove an SOV category | SOV Management MCP |
| Which client should this run against | Authorization MCP |

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

The expiry you choose is fixed. The token does not renew itself. When that date passes, the client can no longer call Pacvue until you create a new token and replace the old value. Up to 50 active tokens per user. Tokens are stored hash-only. Pacvue cannot extend or recover a token.

#### Renew an expired token

Do this when the expiry you set is reached, or a few days before.

1. Sign in to the Pacvue Console and open **Settings → MCP**.
2. Click **Create API Token**, choose a new expiry (max 180 days), and copy the new `pv_...` value immediately.
3. Replace the old token in the MCP client config. Keep the raw token, with no `Bearer` prefix.
4. Fully quit and restart the MCP client.
5. Delete the expired token on the same Settings page.

To revoke a token before it expires, delete it on that page. Deleting it does not create a replacement. You still need a new token in the client.

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

You should see the services in [What you get](#what-you-get) that your account is entitled to. A missing service usually means the account does not have that product, not a bad config.

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

Call `list_query_platforms` for the platforms on this account. Do not assume a platform from a previous conversation.

### Brand Analytics

`fetch_brand_analytics_search_terms` returns Amazon and Walmart category search terms. It does not follow the Data Query flow.

### SOV Query MCP

| Tool | What it does |
| --- | --- |
| `list_sov_material` | Resolve categories, brands, keyword tags, keywords, Walmart zip codes, and stores. |
| `query_sov` | Return a `brand`, `keyword`, or `asin` tab as paginated JSON. |

Flow: `list_sov_material` → `query_sov`.

Required on `query_sov`: `platform`, `tab` (`brand`, `keyword`, or `asin`), `categoryIds`, `startDate`, `endDate`.

`deviceMode` and extra filters depend on the platform. Take the allowed values from `list_sov_material` rather than assuming them.

For a spreadsheet or a multi-tab ZIP, use Report MCP's SOV report instead of `query_sov`.

### Budget Manager MCP

| Tool | What it does |
| --- | --- |
| `list_budget_platforms` | Platforms, profile labels, and budget-line sub-types. |
| `fetch_budget_vs_spend` | Monthly budget, spend, pacing, and utilization by profile, and by tag where the platform uses tags. |

This is Budget Manager data, not an ad-performance report. Use Report MCP or Data Query MCP for campaign metrics. Call `list_budget_platforms` for the platforms on this account.

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

Call `list_campaign_mgmt_platforms` for the platforms you can change. The schema call is the source of truth for fields. On Amazon, one write request can contain only one ad product (Sponsored Products, Sponsored Brands, Sponsored Display, or Sponsored TV). Split mixed updates into separate actions.

### Tag Management MCP

Creates and edits tags, and adds or removes the campaigns, keywords, products, or other items bound to a tag. The agent must restate the change and wait for your confirmation before `manage_tag`, `delete_tag`, or `apply_tag_item_change`.

| Tool | What it does |
| --- | --- |
| `list_tag_capabilities` | Platforms and tag types you can use, and what each one allows. Call this first. |
| `list_tags` | List or look up tags for one platform and tag type. |
| `query_tag_items` | Items on a tag, or the tags on an item. |
| `manage_tag` | Create tags, or rename, set a target, or change the owner of one tag. |
| `delete_tag` | Delete one tag. Deleting a parent also deletes its sub tags and their item bindings. |
| `preview_tag_item_change` | Dry run of adding or removing items. Nothing is written. Returns a `changeToken`. |
| `apply_tag_item_change` | Apply the preview you confirmed, using that `changeToken`. |

Call `list_tag_capabilities` for the platforms and tag types on your account. Take ids and item fields from that response.

To change which items are on a tag: `list_tag_capabilities` → `list_tags` or `query_tag_items` → `preview_tag_item_change` → you confirm the plan → `apply_tag_item_change`. There is no apply without a preview. The `changeToken` expires 15 minutes after the preview. If it expires, or the plan no longer matches what you saw, preview again.

Deleting a tag is not reversible. The agent must show the tag name, any sub tags, and how many items are bound, and you must confirm that specific tag before `delete_tag` runs.

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

**Tag Management**

```
Add the campaign "Brand - Exact" to my campaign tag "Hero".
Show me the preview and wait for my confirmation before applying.
```

**AMC**

```
On my AMC instance, draft SQL for shoppers who viewed but did not purchase in the last 30 days.
Show me the SQL and wait before you submit a report.
```

The agent discovers the schema at runtime. For a file export it polls until the link is ready. For a write it stops and asks you to confirm.

## Troubleshooting

### API token expired

An API token stops working on the expiry date you chose when you created it. Nothing extends it automatically. Follow [Renew an expired token](#renew-an-expired-token): create a new token, paste it over the old `pv_...` value, restart the client, then delete the expired token.

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

Restart the client and confirm the token or OAuth session. The tool list is also limited to products and retailers on your Console account.

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
| API token lifetime | The expiry you set, up to 180 days. Renew it when that date arrives. |
| OAuth refresh token | 7-day sliding window |
