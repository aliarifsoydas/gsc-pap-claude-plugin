# GSC PAP: Google Search Console for Claude

Ask Claude about your Google Search Console data: top queries, decaying pages, ranking trends, quick wins, cannibalization, Core Web Vitals, and GA4 conversions per landing page.

**Free. 34 read-only tools. Hosted, so there is no Google Cloud project, no Python and no JSON credentials to manage.** You sign in with Google at [gscpap.com](https://gscpap.com) and connect; the OAuth client is already registered, for everyone.

| | |
|---|---|
| Price | Free, $0. No credit card, no trial clock, no paid tier today. [Pricing](https://gscpap.com/pricing) |
| Tools | 34, all read-only |
| Data sources | Search Console, Google Analytics 4, PageSpeed Insights, live Google SERPs |
| MCP endpoint | `https://mcp.gscpap.com/mcp` (streamable HTTP) |
| Auth | Google sign-in. OAuth 2.0 with Dynamic Client Registration for connectors, or a bearer token for config-file clients |
| Scopes | `webmasters.readonly`, plus `analytics.readonly` if you connect GA4. It cannot change a property, submit a sitemap or remove a URL |
| History | A Personal Archive copies the 16 months Google still has when you connect, then keeps every new day, so your history grows past Google's 16-month limit |

## What it adds

A single MCP server (`gsc-pap`) exposing 34 tools over the Search Console, Google Analytics 4, PageSpeed Insights and SERP APIs:

**Spot quick SEO wins**
- `find_quick_wins`: queries at positions 5 to 20 with at least 100 impressions but under 5% CTR, which is where a title or snippet rewrite pays
- `find_cannibalization`: several of your URLs competing for one query, splitting the clicks

**Find what is losing traffic**
- `find_content_decay`: pages losing organic traffic over time
- `historical_position_trend`: one query's position over the last N days, trending up, down or just volatile
- `compare_periods`: diff any two windows, for example the last 28 days against the prior 28

**Beat the 16-month limit**
- `search_analytics_archive`: query your full history, past Google's 16-month retention
- `archive_status`: per-site coverage, earliest and latest day, row count, last run

**See the SERP your rankings live in**
- `serp_top_results`: the live top organic results for a query and country, plus which SERP features are present (AI Overview, featured snippet, map pack, People Also Ask)
- `serp_competitor_analysis`: where your domain sits for a query and the full top 10 around it
- `serp_features_audit`: up to 25 queries at once, to explain a low CTR at a good position

**Diagnose performance and Core Web Vitals**
- `pagespeed_insights`: Lighthouse audit, the four scores, Core Web Vitals and the top opportunities
- `pagespeed_field_data`: real-user CrUX metrics (LCP, INP, CLS at p75) for a URL and for the origin

**Diagnose indexing issues**
- `get_index_coverage_summary`: account-wide breakdown of indexing states
- `inspect_url`: one URL in depth, straight from the URL Inspection API
- `batch_inspect_urls`: bulk URL inspection, up to 2,000 URLs

**Pull performance data**
- `get_performance_overview`: clicks, impressions, CTR and position in one line
- `search_analytics_query`: the whole Search Analytics surface, any dimension and filter
- `get_search_by_page_query`: the query mix behind one page

**Connect search to engagement and revenue (GA4)**
- `list_ga4_properties`: find the property_id the other GA4 tools need
- `ga4_overview`: users, sessions, views, bounce and engagement, with a daily breakdown
- `ga4_top_pages`: top landing pages by sessions
- `ga4_traffic_sources`: channel, source, medium, campaign
- `ga4_realtime`: active users in the last 30 minutes
- `ga4_conversions_by_source`: source and medium with conversions and revenue
- `ga4_user_demographics`: country, device and browser slices
- `ga4_engagement`: engagement rate, duration and bounce per page
- `ga4_events_by_name`: scroll depth, outbound clicks, downloads and custom events, optionally per page
- `ga4_compare_periods`: diff any window for any GA4 metric
- `correlate_gsc_to_ga4`: the cross-tool one. Search Console clicks per page joined to GA4 sessions, conversions and revenue for that same page

**Manage sites and sitemaps**
- `list_sites`, `get_site_details`: your verified properties
- `list_sitemaps`, `get_sitemap_details`: sitemap health
- `detect_duplicate_properties`: domain and URL-prefix properties that split your reporting in two

Use it in chat:

> Show me my 5 worst-decaying pages last 30 days, with the queries they used to rank for.

> Which pages are stuck at position 11 to 20 with 1k+ impressions? Suggest content tweaks for the top 3.

> Run a Lighthouse audit on https://example.com/pricing and list the top 3 opportunities by potential ms savings.

> Pull my Search Console data from January 2024, which is past Google's 16-month retention.

> Correlate my Search Console pages with GA4 conversions for the last 28 days. Which top-clicked pages bring zero conversions?

> Why is my CTR 1% at position 4 for this query? Check what else is on that SERP.

## Install

### Claude.ai or ChatGPT: add it as a connector (no token, no file)

1. Open the connector settings in your client. In Claude.ai that is your avatar, then Settings, then Connectors, then "Add custom connector"
2. Paste the URL `https://mcp.gscpap.com/mcp`
3. Approve the Google sign-in when the client opens it

The server implements OAuth 2.0 with Dynamic Client Registration (RFC 7591), so the client registers itself. Step by step, with screenshots: [Add a Search Console connector to Claude.ai](https://gscpap.com/integrations/claude-ai-connectors).

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

Setup guides for the other clients (Cursor, VS Code with Copilot, Codex CLI, Gemini CLI, Windsurf, Zed, JetBrains, Continue, plain HTTP) are at [gscpap.com/integrations](https://gscpap.com/integrations).

## How it works

- **Hosted bridge**: `mcp.gscpap.com` exposes a streamable-HTTP MCP endpoint. We do the OAuth exchange with Google for you and store an encrypted refresh token.
- **Per-site allowlist**: Google's scope is account-wide (`webmasters.readonly`), so the server adds a per-user, per-site authorization layer on top. You decide which properties Claude can see.
- **Per-token site scope**: each token can be narrowed to a subset of your authorized sites, at [app.gscpap.com/account/tokens](https://app.gscpap.com/account/tokens) under **Edit access**. Useful for per-client tokens, a scoped CI token, or limiting the blast radius if a token leaks.
- **Rate limits**: 30 requests per minute and 500 per hour per token, 60 per minute and 1,000 per hour for the account across all its tokens. Live usage is on [app.gscpap.com/account](https://app.gscpap.com/account).
- **Audit log**: every tool call is recorded in your own account.
- **Read-only**: nothing in the surface can modify your sites, sitemaps or settings.

## Privacy and data

- Your Google refresh token is encrypted at rest with AES-256-GCM
- Aggregate analytics through Google Analytics 4 with IP anonymisation. No marketing pixels, no third-party ad networks. See the [Privacy Policy](https://gscpap.com/privacy)
- Your Search Console and Analytics data is never sold, shared or used to train anything
- Revoke at any time from [app.gscpap.com/account/tokens](https://app.gscpap.com/account/tokens), or from your Google account permissions

## Pricing

Free. $0, no credit card, no trial expiry and no feature behind an upgrade prompt. The only ceilings are the rate limits above. Paid plans arrive at v1.0, with at least 60 days notice and a free tier for light use. The full answer, limits included, is at [gscpap.com/pricing](https://gscpap.com/pricing).

## Support

- Guides: https://gscpap.com/guides
- All tools: https://gscpap.com/tools
- FAQ: https://gscpap.com/faq
- Issues: https://github.com/aliarifsoydas/gsc-pap-claude-plugin/issues
