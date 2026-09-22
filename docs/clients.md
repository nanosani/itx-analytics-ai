# Connecting clients

Replace `https://your-site.com` with your site. The endpoint and access keys come from WordPress → ITX Analytics → Settings → Connect an AI assistant.

Endpoint: `https://your-site.com/wp-json/itx-analytics/v1/mcp`

## The setup prompt (Claude Code, Cursor, Codex, agents)

Create an access key on the settings card and copy the **setup prompt** it shows. Paste it into the tool; it contains the endpoint, the header and instructions for each tool, and ends by calling `get_site_info` to confirm. Everything below is the manual equivalent.

## claude.ai (web) and Claude Desktop

1. Settings → Connectors → **Add custom connector**.
2. Name: *ITX Analytics*. URL: the endpoint. Leave client id/secret empty (the server supports dynamic registration).
3. Click Connect. A WordPress sign-in opens, then the consent screen. Choose whether to allow annotations/goals, click **Allow**.
4. In a chat, enable the connector and ask: *"Give me a weekly review of the site."*

Connectors work in the Claude web app, the desktop apps and the mobile apps once added. Revoke from the settings card under *Connected assistants*.

## ChatGPT

1. Settings → Connectors → **Create** (requires Developer mode for custom MCP servers).
2. Name: *ITX Analytics*. MCP server URL: the endpoint. Authentication: **OAuth**.
3. Sign in to WordPress and approve.
4. In a chat, choose the connector under Tools, or use it in Deep Research.

## Claude Code

Either install the plugin (recommended, adds the skills):

```bash
claude plugin marketplace add nanosani/itx-analytics-ai
claude plugin install itx-analytics@itx-analytics-ai
```

or add the server directly with an access key:

```bash
claude mcp add --transport http itx-analytics "https://your-site.com/wp-json/itx-analytics/v1/mcp" --header "Authorization: Basic <ACCESS-KEY>"
```

Or with OAuth (no key; Claude Code opens the browser on first use):

```bash
claude mcp add --transport http itx-analytics "https://your-site.com/wp-json/itx-analytics/v1/mcp"
```

## Cursor

`.cursor/mcp.json` in the project (or the global one):

```json
{
  "mcpServers": {
    "itx-analytics": {
      "url": "https://your-site.com/wp-json/itx-analytics/v1/mcp",
      "headers": { "Authorization": "Basic <ACCESS-KEY>" }
    }
  }
}
```

Without `headers`, Cursor runs the OAuth flow instead.

## Codex CLI

`~/.codex/config.toml`:

```toml
[mcp_servers.itx-analytics]
url = "https://your-site.com/wp-json/itx-analytics/v1/mcp"
http_headers = { Authorization = "Basic <ACCESS-KEY>" }
```

## VS Code (Copilot agent mode), Windsurf, other JSON-configured clients

```json
{
  "servers": {
    "itx-analytics": {
      "type": "http",
      "url": "https://your-site.com/wp-json/itx-analytics/v1/mcp",
      "headers": { "Authorization": "Basic <ACCESS-KEY>" }
    }
  }
}
```

(VS Code uses `servers`; most others use `mcpServers`.)

## Scripts and curl

```bash
curl -X POST https://your-site.com/wp-json/itx-analytics/v1/mcp \
  -H "Authorization: Basic <ACCESS-KEY>" \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"get_overview","arguments":{"period":"last_7_days"}}}'
```

## Troubleshooting

- **401 with `WWW-Authenticate`** — no or bad credentials. Create a new access key, or reconnect the OAuth client.
- **401 on a site with an access key that should work** — some hosts strip the `Authorization` header before PHP sees it. Add to the site's `.htaccess`, above the WordPress block: `RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]`. On nginx + PHP-FPM make sure `fastcgi_pass_header Authorization;` is set.
- **404 from the endpoint** — AI access is switched off on the settings card, or pretty permalinks are off (then use `?rest_route=/itx-analytics/v1/mcp`).
- **OAuth never completes** — the site must be HTTPS; the assistant's redirect URL must be https or localhost; check that `/.well-known/oauth-authorization-server` on your domain returns JSON (a security plugin or CDN rule may block dot-paths).
- **Tools list is short** — the free edition has the core tools; page reports, journeys, e-commerce and the write tools are Pro.
