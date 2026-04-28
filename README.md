# GSC PAP — Google Search Console for Claude Code

Ask Claude about your Google Search Console data — top queries, decaying pages, ranking trends, quick wins, content cannibalization. **Plug & play hosted MCP, no GCP project, no Python, no JSON files to manage.**

## What it adds

A single MCP server (`gsc-pap`) exposing 18 tools over the Google Search Console API:

- `get_performance_overview` — clicks, impressions, position over a window
- `find_quick_wins` — pages ranking 5–20 with high impressions (best ROI for optimization)
- `find_content_decay` — pages losing clicks vs. a baseline window
- `find_cannibalization` — multiple pages competing for the same query
- `compare_periods` — week-over-week, month-over-month deltas
- `historical_position_trend` — long-term position trajectory for a query/page
- `inspect_url`, `batch_inspect_urls` — index coverage + mobile usability checks
- `list_sitemaps`, `get_sitemap_details` — sitemap health
- `search_analytics_query`, `search_analytics_archive` — raw + archived queries
- `list_sites`, `get_site_details` — verified property management
- `detect_duplicate_properties` — find domain/URL property duplicates
- `get_index_coverage_summary`, `get_search_by_page_query` — coverage diagnostics
- `archive_status` — view archived data extent

Use it in chat:

> Show me my 5 worst-decaying pages last 30 days, with the queries they used to rank for.

> Which pages are stuck at position 11–20 with 1k+ impressions? Suggest content tweaks for the top 3.

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
