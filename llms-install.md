# Installing GSCPAP (for AI agents such as Cline)

GSCPAP is a hosted, remote MCP server. There is nothing to clone, build or run locally: the agent only adds one server entry that points at `https://mcp.gscpap.com/mcp`.

## 1. Get a token (the user does this once)

Ask the user to:

1. Open https://app.gscpap.com/login and sign in with Google. GSCPAP asks only for read-only scopes: `webmasters.readonly`, plus `analytics.readonly` if they connect Google Analytics 4.
2. Choose which Search Console sites the assistant may read.
3. Open https://app.gscpap.com/account/tokens, click **+ New token** and copy it. It is shown once.

Do not invent a token and do not ask for a Google password. The user pastes the token to you.

## 2. Add the server

For Cline, edit `cline_mcp_settings.json` (in VS Code: MCP Servers icon in the Cline sidebar, then Configure, then Configure MCP Servers; for the Cline CLI: `~/.cline/data/settings/cline_mcp_settings.json`) and add:

```json
{
  "mcpServers": {
    "gsc-pap": {
      "type": "streamableHttp",
      "url": "https://mcp.gscpap.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_TOKEN"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

Replace `YOUR_TOKEN` with the user's token. Keep `"type": "streamableHttp"`: without it Cline falls back to the legacy SSE transport and the server shows in the list but never connects.

With the Cline CLI the same thing is one command:

```bash
cline mcp add gsc-pap https://mcp.gscpap.com/mcp \
  --transport http \
  --header "Authorization: Bearer $GSC_PAP_TOKEN" \
  --yes
```

Other clients (Claude Code, Cursor, VS Code, Claude Desktop and more) are covered in the README and at https://gscpap.com/integrations.

## 3. Verify

Call the `list_sites` tool. It returns the Search Console properties the user authorized. An empty list means no site was authorized yet: send the user to https://app.gscpap.com/account/sites.

If a call fails with "Missing or invalid bearer token", the token is wrong or was revoked; ask the user for a new one from https://app.gscpap.com/account/tokens.

All 38 tools are read-only. None of them can change a site, submit a sitemap or remove a URL.
