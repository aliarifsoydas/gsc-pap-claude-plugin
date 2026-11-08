# GSCPAP - Google Search Console & GA4 MCP Server for Claude, ChatGPT and Cursor

Ask Claude about your Google Search Console data: top queries, decaying pages, ranking trends, quick wins, cannibalization, Core Web Vitals, and GA4 conversions per landing page.

**Free. 39 tools, read-only on your Google data. Hosted, so there is no Google Cloud project, no Python and no JSON credentials to manage.** You sign in with Google at [gscpap.com](https://gscpap.com) and connect; the OAuth client is already registered, for everyone.

| | |
|---|---|
| Price | Free, $0. No credit card, no trial clock, no paid tier today. [Pricing](https://gscpap.com/pricing) |
| Tools | 38, all read-only |
| Data sources | Search Console, Google Analytics 4, PageSpeed Insights, live Google SERPs |
| MCP endpoint | `https://mcp.gscpap.com/mcp` (streamable HTTP) |
| Auth | Google sign-in. OAuth 2.0 with Dynamic Client Registration for connectors, or a bearer token for config-file clients |
| Scopes | `webmasters.readonly`, plus `analytics.readonly` if you connect GA4. It cannot change a property, submit a sitemap or remove a URL |
| History | A Personal Archive copies the 16 months Google still has when you connect, then keeps every new day, so your history grows past Google's 16-month limit |
| Registry | [`com.gscpap/gsc-pap`](https://registry.modelcontextprotocol.io/v0/servers?search=com.gscpap) in the official MCP Registry |

## What's new

- **2026-11-08** `feature_request`: ask your assistant to file what GSC PAP cannot do yet, or vote for an existing request; voters get an email when it ships. Roadmap at app.gscpap.com/account/roadmap
- **2026-11-02** `ga4_ai_traffic`: the visits ChatGPT, Perplexity, Gemini, Claude and Copilot send you, per engine, against Google organic, and which pages they land on. It also counts the assistant visits GA4 files under Referral instead of its AI Assistant channel
- **2026-11-02** `find_ai_overview_suspects`: queries whose CTR fell while their position held, the mark an AI Overview leaves in Search Console
- **2026-11-02** SERP tools report a feature as unavailable instead of absent when the data is missing, so a missing AI Overview is never read as proof there is none
- **2026-10-31** Days Search Console returned empty are fetched again two days later, so the archive no longer keeps a day Google had not finalised
- **2026-10-31** When the Google connection stops working, the assistant is told so, gets the reconnect link, and knows which tools still answer from the archive

Every change, with dates: [gscpap.com/changelog](https://gscpap.com/changelog)

## What it adds

A single MCP server (`gsc-pap`) exposing 39 tools over the Search Console, Google Analytics 4, PageSpeed Insights and SERP APIs:

**Spot quick SEO wins**
- `find_quick_wins`: queries at positions 5 to 20 with at least 100 impressions but under 5% CTR, which is where a title or snippet rewrite pays
- `find_cannibalization`: several of your URLs competing for one query, splitting the clicks

**Find what is losing traffic**
- `find_content_decay`: pages losing organic traffic over time
- `historical_position_trend`: one query's position over the last N days, trending up, down or just volatile
- `compare_periods`: diff any two windows, for example the last 28 days against the prior 28
- `find_dead_pages`: pages appearing for months that have never earned a click, split by whether Google shows them and nobody clicks or barely shows them at all

**Beat the 16-month limit**
- `search_analytics_archive`: query your full history, past Google's 16-month retention
- `archive_status`: per-site coverage, earliest and latest day, row count, last run

**See the SERP your rankings live in**
- `serp_top_results`: the live top organic results for a query and country, plus which SERP features are present (featured snippet, map pack, People Also Ask; AI Overviews are reported as unknown)
- `serp_competitor_analysis`: where your domain sits for a query and the full top 10 around it
- `serp_features_audit`: up to 25 queries at once, to explain a low CTR at a good position

**Diagnose performance and Core Web Vitals**
- `pagespeed_insights`: Lighthouse audit, the four scores, Core Web Vitals and the top opportunities
- `pagespeed_field_data`: real-user CrUX metrics (LCP, INP, CLS at p75) for a URL and for the origin, in about a second, with an optional weekly trend of the last 25 weeks

**Diagnose indexing issues**
- `get_index_coverage_summary`: account-wide breakdown of indexing states
- `inspect_url`: one URL in depth, straight from the URL Inspection API
- `batch_inspect_urls`: bulk URL inspection, up to 2,000 URLs
- `find_new_pages`: pages Search Console started showing this week, and whether each has earned a click yet

**Pull performance data**
- `get_performance_overview`: clicks, impressions, CTR and position in one line, for any date range or the last N days
- `search_analytics_query`: the whole Search Analytics surface, any dimension and filter
- `get_search_by_page_query`: the query mix behind one page

**See how AI assistants send you traffic (GEO)**
- `ga4_ai_traffic`: visits from ChatGPT, Perplexity, Gemini, Claude and Copilot, per engine, against organic, and the pages they send people to; `property_id="all"` compares every site
- `find_ai_overview_suspects`: queries whose CTR fell while their position held, the footprint an AI Overview leaves

**Connect search to engagement and revenue (GA4)**

Every GA4 report tool takes `days`, or an exact `start_date` and `end_date`.

- `list_ga4_properties`: find the property_id the other GA4 tools need
- `ga4_overview`: users, sessions, views, bounce and engagement, with a daily breakdown
- `ga4_top_pages`: top landing pages by sessions
- `ga4_traffic_sources`: channel, source, medium, campaign
- `ga4_realtime`: active users in the last 30 minutes
- `ga4_conversions_by_source`: source and medium with conversions and revenue
- `ga4_user_demographics`: country, device and browser slices
- `ga4_engagement`: engagement rate, duration and bounce per page
- `ga4_events_by_name`: scroll depth, outbound clicks, downloads and custom events, optionally per page
- `ga4_compare_periods`: diff any window for any GA4 metric, including an exact start and end date
- `correlate_gsc_to_ga4`: the cross-tool one. Search Console clicks per page joined to GA4 sessions, conversions and revenue for that same page

**Manage sites and sitemaps**
- `list_sites`, `get_site_details`: your verified properties
- `list_sitemaps`, `get_sitemap_details`: sitemap health
- `detect_duplicate_properties`: domain and URL-prefix properties that split your reporting in two

**Shape the roadmap**
- `feature_request`: vote for or file feature requests from the chat. The assistant asks once whether it may report gaps by itself; until you say yes it shows you the text first. Links and site data are stripped

Use it in chat:

> Show me my 5 worst-decaying pages last 30 days, with the queries they used to rank for.

> Which pages are stuck at position 11 to 20 with 1k+ impressions? Suggest content tweaks for the top 3.

> Run a Lighthouse audit on https://example.com/pricing and list the top 3 opportunities by potential ms savings.

> Pull my Search Console data from January 2024, which is past Google's 16-month retention.

> Correlate my Search Console pages with GA4 conversions for the last 28 days. Which top-clicked pages bring zero conversions?

> Why is my CTR 1% at position 4 for this query? Check what else is on that SERP.

## Install

### Claude.ai: add it as a connector (no token, no file)

1. Open the connector settings in Claude.ai: your avatar, then Settings, then Connectors, then "Add custom connector"
2. Paste the URL `https://mcp.gscpap.com/mcp`
3. Approve the Google sign-in when the client opens it

The server implements OAuth 2.0 with Dynamic Client Registration (RFC 7591), so the client registers itself. Step by step, with screenshots: [Add a Search Console connector to Claude.ai](https://gscpap.com/integrations/claude-ai-connectors).

ChatGPT supports the same flow, but custom MCP connections there are a Business and Enterprise/Edu feature on ChatGPT web, which is OpenAI's rule rather than ours: [Connect ChatGPT to Google Search Console](https://gscpap.com/integrations/chatgpt).

### Claude Code: let Claude install it (about 30 seconds)

1. Open [gscpap.com](https://gscpap.com) and sign in with Google (read-only Search Console scope)
2. Pick which sites to grant access to. This is a per-site allowlist on top of the OAuth scope
3. On [app.gscpap.com/account/tokens](https://app.gscpap.com/account/tokens), hit **+ New token**, then click **Copy install prompt** under the Claude Code tab
4. Paste that prompt into any Claude Code session. Claude runs the install command, verifies it, and lists your sites so you can see that it worked

### Manual install

If you would rather drive the CLI yourself:

```bash
claude mcp add --transport http --scope user gsc-pap https://mcp.gscpap.com/mcp \
  --header "Authorization: Bearer YOUR_TOKEN_HERE"
```

This plugin variant reads a `${GSC_PAP_TOKEN}` env var instead. Set it once:

```bash
# macOS / Linux
echo 'export GSC_PAP_TOKEN="paste-your-token-here"' >> ~/.zshrc
source ~/.zshrc

# Windows (PowerShell, persistent)
[Environment]::SetEnvironmentVariable("GSC_PAP_TOKEN", "paste-your-token-here", "User")
```

Restart Claude Code, or run `claude mcp list` to check, and the `gsc-pap` server shows up as Connected.

### Cursor

Create a token at [app.gscpap.com/account/tokens](https://app.gscpap.com/account/tokens), then add this to `~/.cursor/mcp.json` (Windows: `%USERPROFILE%\.cursor\mcp.json`):

```json
{
  "mcpServers": {
    "gsc-pap": {
      "url": "https://mcp.gscpap.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_TOKEN"
      }
    }
  }
}
```

Full guide: [Connect Cursor to Google Search Console](https://gscpap.com/integrations/cursor).

### VS Code (GitHub Copilot)

Add this to `.vscode/mcp.json`. VS Code asks for the token once and stores it as a secret:

```json
{
  "servers": {
    "gsc-pap": {
      "type": "http",
      "url": "https://mcp.gscpap.com/mcp",
      "headers": {
        "Authorization": "Bearer ${input:gsc_pap_token}"
      }
    }
  },
  "inputs": [
    {
      "type": "promptString",
      "id": "gsc_pap_token",
      "description": "GSC PAP bearer token (from app.gscpap.com/account/tokens)",
      "password": true
    }
  ]
}
```

Full guide: [Connect GitHub Copilot in VS Code to Google Search Console](https://gscpap.com/integrations/vscode-copilot).

### Claude Desktop

Claude Desktop reaches remote servers through `mcp-remote`, so it needs Node.js. Add this to `~/Library/Application Support/Claude/claude_desktop_config.json` (Windows: `%APPDATA%\Claude\claude_desktop_config.json`) and restart Claude Desktop:

```json
{
  "mcpServers": {
    "gsc-pap": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.gscpap.com/mcp", "--header", "Authorization:${AUTH}"],
      "env": {
        "AUTH": "Bearer YOUR_TOKEN"
      }
    }
  }
}
```

Full guide: [Connect Claude Desktop to Google Search Console](https://gscpap.com/integrations/claude-desktop). You can also skip the file and add it as a connector, as in the Claude.ai section above.

### Other clients

Setup guides for the other clients (Cursor, VS Code with Copilot, Cline, opencode, Codex CLI, Gemini CLI, Windsurf, Zed, JetBrains, Continue, Warp, Goose, LM Studio, ChatGPT, plain HTTP) are at [gscpap.com/integrations](https://gscpap.com/integrations).

## Troubleshooting

- **"Missing or invalid bearer token"**: the token in your config is wrong or was revoked. Create a new one at [app.gscpap.com/account/tokens](https://app.gscpap.com/account/tokens) and paste it in, with `Bearer ` and a space in front where the config asks for the whole header.
- **The server does not show up after editing the config**: check the file for JSON mistakes such as a trailing comma, check that `gsc-pap` sits inside `mcpServers` (`servers` in VS Code), then restart the client.
- **Claude Desktop: "npx: command not found" or the server never starts**: Node.js is missing or not on Claude Desktop's PATH. Install the LTS from nodejs.org and restart.
- **Tools appear but a site is missing**: the site is not on your allowlist, or the token is scoped to other sites. Check [app.gscpap.com/account/sites](https://app.gscpap.com/account/sites) and the token's **Edit access**.
- **The assistant says the Google connection stopped working**: access was removed in your Google account, the password changed, or the sign-in aged out. Reconnect at [app.gscpap.com/login?reconnect=1](https://app.gscpap.com/login?reconnect=1). Nothing archived is lost.
- **Still stuck**: email support@gscpap.com with the error message.

## How it works

- **Hosted bridge**: `mcp.gscpap.com` exposes a streamable-HTTP MCP endpoint. We do the OAuth exchange with Google for you and store an encrypted refresh token.
- **Per-site allowlist**: Google's scope is account-wide (`webmasters.readonly`), so the server adds a per-user, per-site authorization layer on top. You decide which properties Claude can see.
- **Per-token site scope**: each token can be narrowed to a subset of your authorized sites, at [app.gscpap.com/account/tokens](https://app.gscpap.com/account/tokens) under **Edit access**. Useful for per-client tokens, a scoped CI token, or limiting the blast radius if a token leaks.
- **Rate limits**: 30 requests per minute and 500 per hour per token, 60 per minute and 1,000 per hour for the account across all its tokens. Live usage is on [app.gscpap.com/account](https://app.gscpap.com/account).
- **Usage log**: every tool call is logged and shown in your own account at [app.gscpap.com/account](https://app.gscpap.com/account).
- **Read-only**: nothing in the surface can modify your sites, sitemaps or settings.

## Privacy and data

- Your Google refresh token is encrypted at rest with AES-256-GCM; bearer tokens are stored only as SHA-256 hashes
- Aggregate analytics through Google Analytics 4 with IP anonymisation. No marketing pixels, no third-party ad networks. See the [Privacy Policy](https://gscpap.com/privacy)
- Your data is not sold, rented or traded, and it is not used to train AI models. It is shared only with the providers that run the service, which the Privacy Policy names
- Use of data received from Google APIs follows the [Google API Services User Data Policy](https://developers.google.com/terms/api-services-user-data-policy), including the Limited Use requirements
- Revoke at any time from [app.gscpap.com/account/tokens](https://app.gscpap.com/account/tokens), or from your Google account permissions

## Pricing

Free. $0, no credit card, no trial expiry and no feature behind an upgrade prompt. The only ceilings are the rate limits above. Paid plans arrive at v1.0, with at least 60 days notice and a free tier for light use. The full answer, limits included, is at [gscpap.com/pricing](https://gscpap.com/pricing).

## Support

- Guides: https://gscpap.com/guides
- What changed and when: https://gscpap.com/changelog
- Hosted and self-hosted Search Console MCP servers compared: https://gscpap.com/google-search-console-mcp-servers
- For AI assistants: https://gscpap.com/llms.txt (every page is also Markdown: append `.md` to its URL)
- All tools: https://gscpap.com/tools
- FAQ: https://gscpap.com/faq
- Issues: https://github.com/aliarifsoydas/gsc-pap-claude-plugin/issues
