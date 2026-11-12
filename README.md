# PerfDelta MCP Server

Run PageSpeed scans and read Core Web Vitals, scores, fixes and history for any public website from Claude, ChatGPT, Cursor, VS Code, Gemini CLI or any other MCP client.

PerfDelta is a website speed test and Core Web Vitals monitoring tool built around comparison: real-user field data from the Chrome UX Report, Lighthouse lab metrics, competitor benchmarks and fix recommendations that name the affected files. This repository holds the client configuration for the hosted PerfDelta MCP server. The server itself runs at perfdelta.com, there is nothing to install or host.

- Website: https://perfdelta.com
- API and MCP documentation: https://perfdelta.com/api-docs
- Get a free API key: https://perfdelta.com/register, then https://perfdelta.com/api-keys

## Server details

| | |
|---|---|
| Endpoint | `https://perfdelta.com/api/mcp.php` |
| Transport | Streamable HTTP (JSON-RPC 2.0, stateless POST) |
| Auth | `Authorization: Bearer YOUR_API_KEY` |
| Without key | `initialize`, `ping` and `tools/list` work without a key |

## Plans

- **Free API key:** one scan every 24 hours (mobile and desktop count as one scan), read tools without limit (reports, fixes, changes, history), agent briefs and notes for your own scans.
- **Paid plans (from Solo):** monthly PageSpeed test budget shared with monitoring and the web app, projects, monitoring, competitors and performance budgets via MCP and REST API.

Details: https://perfdelta.com/pricing

## Setup

### Claude Code

```bash
claude mcp add --transport http perfdelta https://perfdelta.com/api/mcp.php \
  --header "Authorization: Bearer YOUR_API_KEY"
```

Or as a plugin from this repository (reads the key from the environment variable `PERFDELTA_API_KEY`):

```bash
/plugin marketplace add PerfDelta/perfdelta-mcp
/plugin install perfdelta@perfdelta
```

### Claude Code project config (`.mcp.json`)

```json
{
  "mcpServers": {
    "perfdelta": {
      "type": "http",
      "url": "https://perfdelta.com/api/mcp.php",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

### Cursor (`.cursor/mcp.json`)

```json
{
  "mcpServers": {
    "perfdelta": {
      "url": "https://perfdelta.com/api/mcp.php",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

### VS Code (`.vscode/mcp.json`)

```json
{
  "servers": {
    "perfdelta": {
      "type": "http",
      "url": "https://perfdelta.com/api/mcp.php",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

### Gemini CLI

```bash
export PERFDELTA_API_KEY=YOUR_API_KEY
gemini extensions install https://github.com/PerfDelta/perfdelta-mcp
```

### Claude.ai, Claude Desktop and ChatGPT

These clients cannot send custom headers. Add a custom connector with the key as URL parameter:

```
https://perfdelta.com/api/mcp.php?key=YOUR_API_KEY
```

Claude: Settings, Connectors, Add custom connector. ChatGPT (Developer Mode): Settings, Connectors, Create, authentication "none". Anyone who knows this URL can use the key, so revoke it at https://perfdelta.com/api-keys if it leaks.

## Tools

| Tool | What it does | Cost |
|---|---|---|
| `scan_url` | Start a scan (mobile, desktop or both) | PageSpeed tests |
| `get_scan` | Status, progress, scores and Core Web Vitals per page | free |
| `get_usage` | Budget and remaining quota | free |
| `get_report` | Latest result for any scanned domain | free |
| `get_report_details` | Full report of your own scans | free |
| `get_fixes` | Prioritized fixes with affected files and elements | free |
| `get_changes` | Changes since the last scan and fixes already applied | free |
| `get_agent_brief` | Markdown work order for coding agents | free |
| `get_history` | Score history of a project URL | free |
| `add_note`, `update_note`, `delete_note` | Notes on a scan, e.g. fix status from a coding agent | free |
| `list_projects`, `get_project`, `create_project` | Projects | free, paid plan required |
| `list_urls`, `add_urls`, `remove_url`, `set_monitoring` | URLs and monitoring | free, paid plan required |
| `list_competitors`, `add_competitor`, `remove_competitor` | Competitor domains | free, paid plan required |
| `set_budget`, `get_budget_status` | Performance budgets and CI checks | free, paid plan required |
| `get_monitoring_events` | Regressions, improvements and budget events (90 days) | free, paid plan required |

With a free key, `tools/list` only returns the tools the free plan can use.

## Example prompts

- "Scan https://example.com on mobile and desktop and summarize the Core Web Vitals."
- "Get the agent brief for my last scan and fix the top three issues in this repository. Report the status of each fix as a note."
- "What changed on example.com since the last scan, and which fixes were applied?"

## Agent workflow

1. `scan_url` measures the page.
2. `get_agent_brief` returns prioritized fixes with stable IDs, affected files and acceptance criteria.
3. The coding agent applies the fixes and reports back per fix with `add_note` ("PD-<id> done: ...").
4. The next scan and brief show the status.

## REST API

The same data is available via REST at `https://perfdelta.com/api/v1`, for example:

```bash
curl -X POST -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com/", "devices": "both"}' \
  "https://perfdelta.com/api/v1/scans.php"
```

Full reference: https://perfdelta.com/api-docs

## About

PerfDelta is built and operated by Christian Schreiber in Berlin, Germany. Lab data comes from the Google PageSpeed Insights API, field data from the Chrome UX Report. PerfDelta is not affiliated with Google.

Contact: info@perfdelta.com
