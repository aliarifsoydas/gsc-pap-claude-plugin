# GSC PAP — Google Search Console for Claude Code

Ask Claude about your Google Search Console data — top queries, decaying pages, ranking trends, quick wins, content cannibalization. **Plug & play hosted MCP, no GCP project, no Python, no JSON files to manage.**

## What it adds

A single MCP server (`gsc-pap`) exposing 30 tools over the Google Search Console + Google Analytics 4 + PageSpeed Insights APIs:

**Spot quick SEO wins**
- `find_quick_wins` — pages ranking positions 11–20 with high impressions (best ROI for optimization)
- `find_cannibalization` — multiple pages competing for the same query, splitting CTR

**Find what's losing traffic**
- `find_content_decay` — pages losing organic traffic over time
- `historical_position_trend` — long-term position trajectory for a query/page
- `compare_periods` — diff any two windows (e.g. last 28 days vs prior 28)

**Beat the 16-month limit**
- `search_analytics_archive` — query your full GSC history beyond Google's 16-month retention
- `archive_status` — per-site archive coverage (earliest/latest date, row count, last run)

**Diagnose performance & Core Web Vitals** *(new in v0.3)*
- `pagespeed_insights` — Lighthouse audit: Performance / Accessibility / Best Practices / SEO scores + CWV + top opportunities
- `pagespeed_field_data` — real-user CrUX metrics (LCP / INP / CLS p75) for URL and origin level

**Diagnose indexing issues**
- `get_index_coverage_summary` — account-wide breakdown of indexing states
- `inspect_url` — single-URL deep dive (URL Inspection API result)
- `batch_inspect_urls` — bulk URL Inspection up to 2,000 URLs

**Pull performance data**
- `get_performance_overview` — clicks, impressions, CTR, position snapshot
- `search_analytics_query` — full Search Analytics API surface (any dimension + filter)
- `get_search_by_page_query` — drill into a page's query mix

**Connect search to engagement & revenue (GA4)** *(new in v0.4)*
- `list_ga4_properties` — discover the GA4 property_id needed for the other GA4 tools
- `ga4_overview` — users / sessions / page views / bounce / engagement, with daily breakdown
- `ga4_top_pages` — top landing pages by sessions
- `ga4_traffic_sources` — by channel / source / medium / campaign
- `ga4_realtime` — last 30 minutes active users
- `ga4_conversions_by_source` — source × medium with conversions + revenue
- `ga4_user_demographics` — country, device, browser slices
- `ga4_engagement` — per-page engagement rate, duration, bounce
- `ga4_compare_periods` — diff any window for any GA4 metric (WoW, MoM)
- `correlate_gsc_to_ga4` — KILLER cross-tool: GSC clicks per page joined to GA4 sessions, conversions, revenue per page

**Manage sites & sitemaps**
- `list_sites`, `get_site_details` — verified property management
- `list_sitemaps`, `get_sitemap_details` — sitemap health
- `detect_duplicate_properties` — find domain/URL property duplicates that split your reporting

Use it in chat:

> Show me my 5 worst-decaying pages last 30 days, with the queries they used to rank for.

> Which pages are stuck at position 11–20 with 1k+ impressions? Suggest content tweaks for the top 3.

> Run a Lighthouse audit on https://example.com/pricing and list the top 3 opportunities by potential ms savings.

> Pull my GSC data from January 2024 — that's beyond Google's 16-month retention.

> Correlate my Search Console pages with GA4 conversions for the last 28 days — which top-clicked pages bring zero conversions?

## Install

### Easiest: let Claude install itself (~30 seconds)

1. Open https://gscpap.com → **Sign in with Google** (read-only Search Console scope)
2. Pick which sites to grant access to (per-site allowlist on top of the OAuth scope)
3. On **https://app.gscpap.com/account/tokens**, hit **+ New token**, then in the install widget click **"Copy install prompt"** under the Claude Code tab
4. Paste that prompt into any Claude Code session — Claude runs the install command itself, verifies, and lists your sites so you know it works

### Manual install

If you prefer to drive the CLI yourself:

```bash
claude mcp add --transport http --scope user gsc-pap https://mcp.gscpap.com/mcp \
  --header "Authorization: Bearer YOUR_TOKEN_HERE"
```

Or via the marketplace once approved:

```bash
claude /plugin install gsc-pap
```

This plugin variant uses a `${GSC_PAP_TOKEN}` env var. Set it once:

```bash
# macOS / Linux
echo 'export GSC_PAP_TOKEN="paste-your-token-here"' >> ~/.zshrc
source ~/.zshrc

# Windows (PowerShell, persistent)
[Environment]::SetEnvironmentVariable("GSC_PAP_TOKEN", "paste-your-token-here", "User")
```

Restart Claude Code (or run `claude mcp list` to verify) and the `gsc-pap` server will appear as Connected.

## How it works

- **Hosted bridge**: `mcp.gscpap.com` exposes a streamable-HTTP MCP endpoint. We do the OAuth dance with Google on your behalf and store an encrypted refresh token.
- **Per-site allowlist**: even though Google's OAuth scope is account-wide (`webmasters.readonly`), our server enforces a per-user, per-site authorization layer on top — you decide which properties Claude can see.
- **Per-token site scope (v0.2)**: each token can be further narrowed to a subset of your authorized sites via **https://app.gscpap.com/account/tokens** → **Edit access**. Useful for per-client tokens, scoped CI tokens, or limiting blast radius if a token leaks.
- **Per-token rate limit (v0.2)**: 30 requests/minute and 500/hour per token. The account-level cap is 60/min and 1000/hour aggregate. Live usage shown on **https://app.gscpap.com/account**.
- **Audit log**: every tool call is logged in your account at https://app.gscpap.com/account.
- **Read-only**: nothing in the API can modify your sites, sitemaps, or settings. Search Console data only.

## Privacy & data

- Your refresh token is encrypted at rest with AES-256-GCM
- Aggregate analytics via Google Analytics 4 with IP anonymisation; no marketing pixels, no third-party ad networks. See [Privacy Policy](https://gscpap.com/privacy)
- Revoke any time at https://app.gscpap.com/account/tokens or directly via Google account permissions

## Pricing

Free during beta. See [gscpap.com/faq](https://gscpap.com/faq) for limits.

## Support

- Docs: https://gscpap.com/guides
- FAQ: https://gscpap.com/faq
- Issues: https://github.com/aliarifsoydas/gsc-pap-claude-plugin/issues
