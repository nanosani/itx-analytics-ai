# ITX Analytics AI

Ask an AI assistant about your WordPress site's traffic.

[ITX Analytics](https://github.com/nanosani/itx-analytics) is a self-hosted, cookieless analytics plugin. From version 0.32 it is also an **MCP server**: Claude, ChatGPT, Cursor, Claude Code, Codex and any other [Model Context Protocol](https://modelcontextprotocol.io) client can read your reports and run custom queries over the aggregated data — on your own server, with no third-party analytics account.

This repository holds the client-side pieces:

- a **Claude Code plugin** (marketplace) that connects to your site and adds analysis skills;
- **setup guides** for claude.ai, Claude Desktop, ChatGPT, Cursor, Codex and generic MCP clients;
- the **tool reference** so you know what an assistant can and cannot see.

The server itself runs inside the plugin on your WordPress site. Nothing is hosted here.

## 1. Switch it on in WordPress

WordPress → **ITX Analytics → Settings → Connect an AI assistant** → tick *Let AI assistants read this site's analytics* → Save.

The card then shows your endpoint, which looks like:

```
https://your-site.com/wp-json/itx-analytics/v1/mcp
```

Two ways to sign in:

| Client | How it authenticates | What you do |
|---|---|---|
| claude.ai, Claude Desktop (remote), ChatGPT, other hosted assistants | OAuth 2.1 | Paste the endpoint as a custom connector. The assistant opens a WordPress sign-in and a consent screen. |
| Claude Code, Cursor, Codex, VS Code, Windsurf, scripts | Access key (a WordPress application password) | Click **Create access key** on the card, copy the snippet for your tool. |

Every connection acts as a WordPress user who can view the dashboard. Access is off by default, keys and connected apps are listed on the card and can be revoked there. Hosted assistants need HTTPS (WordPress only issues application passwords and OAuth over HTTPS).

## 2. Claude Code plugin

```bash
claude plugin marketplace add nanosani/itx-analytics-ai
claude plugin install itx-analytics@itx-analytics-ai
```

You are asked for the endpoint URL and an access key (both from the settings card). The plugin adds the `itx-analytics` MCP server plus these skills:

| Skill | What it does |
|---|---|
| `analytics-basics` | Which tool answers which question, metric definitions, caveats. Loads for any traffic question. |
| `weekly-review` | A two-minute report: totals vs the previous period, what moved and why, actions. |
| `traffic-change` | Diagnose a drop or spike by page, channel, country, device. |
| `content-refresh` | Pages losing search traffic that deserve an update; evergreen winners. |
| `attribution` | Channel mix and trend, top hosts, AI-assistant referrals, campaigns. |
| `page-review` | One page in depth with specific improvements. |
| `ecommerce-health` | Store scorecard, sources that sell, funnel leaks (Pro). |
| `analytics-query` | How to write correct `query` calls for custom breakdowns. |

Try: *"How did the site do this week?"*, *"Why did traffic drop on Tuesday?"*, *"Which posts should I update?"*, *"How much traffic do we get from ChatGPT?"*

## 3. Other clients

See [docs/clients.md](docs/clients.md) for claude.ai, Claude Desktop, ChatGPT, Cursor, Codex, VS Code and generic JSON configuration, and [docs/tools.md](docs/tools.md) for every tool, resource and prompt the server exposes.

## What the assistant can see

- The same aggregated data as the dashboard: totals, time series, pages, sources, countries, devices, events, real time, and (Pro) page reports, journeys, flows, campaigns, content, e-commerce, goals, search queries.
- A constrained `query` tool: metrics × dimensions × filters over the rollup tables. The server builds the SQL; the assistant never writes any.
- **Never** visitor-level rows, IP addresses or identifiers — the plugin does not store them in the first place.
- Writes are limited to two Pro tools (add an annotation, create a goal) and only when you grant the *analytics:write* scope on the consent screen. Nothing can be deleted.

## WordPress 6.9+ and the WordPress MCP Adapter

Every tool is also registered as a [WordPress Ability](https://developer.wordpress.org/apis/abilities-api/) under the `itx-analytics/*` namespace, so sites running the official [WordPress MCP Adapter](https://github.com/WordPress/mcp-adapter) can reach them through its default server as well. The built-in endpoint above works on any WordPress version and exposes the tools directly, so it is the recommended way to connect.

## License

MIT for the contents of this repository. ITX Analytics itself is GPL-2.0-or-later.
