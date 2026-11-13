# RankReactor MCP

Config-only listing package for RankReactor's hosted [Model Context Protocol](https://modelcontextprotocol.io) server. This repository does not contain application or backend code.

[RankReactor](https://www.rankreactor.ai) is a set-and-forget SEO product. You add a live website as a project. RankReactor reads the site, then can generate SEO articles and (on higher plans) UGC-style videos, and it tracks keywords, analytics, technical SEO, and a site audit.

**MCP URL (use this exact host):** `https://www.rankreactor.ai/mcp`

Always use `www`. The bare domain redirects and is not the MCP endpoint.

| | |
| --- | --- |
| Transport | Streamable HTTP |
| Auth | OAuth 2.1 in the browser (dynamic client registration and CIMD, PKCE S256). A dashboard project API key is still required. OAuth keeps that key out of client config: paste it on **Connect RankReactorAI**. Clients that do not support OAuth can send the same key as a remote HTTP `Authorization: Bearer` header. |
| Account | A RankReactor account is required. A [Free](https://www.rankreactor.ai) plan is available. |
| Privacy | [Privacy policy](https://www.rankreactor.ai/privacy-policy) |
| Terms | [Terms of service](https://www.rankreactor.ai/tos) |
| Support | [support@rankreactor.ai](mailto:support@rankreactor.ai) |

Plans: **Free** (1 lifetime article per project), **Starter** $39/mo, **Pro** $79/mo, **Ultra** $159/mo. UGC video is available on Pro and Ultra.

The hosted tools let an assistant read project status and stored SEO metrics, list generated articles and videos, refresh metrics, and queue new articles or UGC videos (write actions ask for confirmation). The current tool list is documented at [Use RankReactor with your AI assistant](https://www.rankreactor.ai/docs/ai-assistants).

## Connect

Do this before adding the MCP URL:

1. Create a RankReactor account if you need one. A [Free](https://www.rankreactor.ai) plan is available.
2. Create or select the project the assistant should use.
3. Open **API Key** in the dashboard sidebar and choose **Generate**. Copy the key now; RankReactor shows the full value once.

Then add `https://www.rankreactor.ai/mcp` in your client. An OAuth-capable client opens **Connect RankReactorAI** in the browser. Paste the project key and choose **Allow**. That window is not a Google, magic-link, or other RankReactor account sign-in.

OAuth keeps the project key out of client config (the files in this repo only contain the public URL). It does not remove the key-generation step. Regenerating or revoking the key in the dashboard stops existing connections until you paste the new key.

Clients that do not support MCP OAuth can use the same hosted Streamable HTTP URL with `Authorization: Bearer <your-project-key>`. Use that placeholder or the client's own secret prompt; do not commit a live key. JSON field names vary by client (`url`, `httpUrl`, or `type: http`). This repository does not ship a local stdio server.

## Claude (claude.ai custom connector)

Remote MCP custom connectors work on Claude, Cowork, and Claude Desktop ([Anthropic help](https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp)). Free Claude accounts can add one custom connector.

**One-click:** [Add RankReactor in Claude](https://claude.ai/customize/connectors?modal=add-custom-connector&connectorName=RankReactor&connectorUrl=https%3A%2F%2Fwww.rankreactor.ai%2Fmcp)

**Pro / Max**

1. Open **Customize → Connectors**.
2. Click **+**, then **Add custom connector**.
3. URL: `https://www.rankreactor.ai/mcp`
4. Leave Advanced settings empty unless Claude asks for a client id.
5. Click **Add**. When **Connect RankReactorAI** opens, paste the project key and choose **Allow**.

**Team / Enterprise:** an Owner adds the URL under **Organization settings → Connectors** (**Add → Custom → Web**). Members then open **Customize → Connectors** and click **Connect**. When **Connect RankReactorAI** opens, paste the project key and choose **Allow**.

Enable the connector per conversation from **+ → Connectors**.

## Claude Code

From [Claude Code MCP docs](https://code.claude.com/docs/en/mcp):

```bash
claude mcp add --transport http rankreactor https://www.rankreactor.ai/mcp
```

That writes a local-scope entry. Use `--scope user` for every project, or `--scope project` to write `.mcp.json` in the repo.

Then run `claude`, type `/mcp`, select `rankreactor`, and **Authenticate**. When **Connect RankReactorAI** opens, paste the project key and choose **Allow**.

## Grok (grok.com)

From [xAI Connectors](https://docs.x.ai/grok/connectors):

1. Open [grok.com/connectors](https://grok.com/connectors).
2. Click **New Connector**, then **Custom**.
3. Enter `https://www.rankreactor.ai/mcp`. When **Connect RankReactorAI** opens, paste the project key and choose **Allow**.

On Grok Business / Enterprise, a team admin may need to provision the connector first in [console.x.ai](https://console.x.ai) (**Grok Business → Connectors → Add Connector → Other**).

A Grok Build marketplace listing is a separate follow-up (see the pull request for the catalog entry text). You can still point Grok Build at this repo once it is listed, or add the same URL as a custom connector.

## Cursor

The Cursor plugin uses a RankReactor project API key, not OAuth. Get the key from the RankReactor dashboard (**API Key** page). Do not paste it into chat or commit it.

1. Install the RankReactorAI plugin (`rankreactorai`) from this GitHub repo. In Cursor, open **Customize**, paste `https://github.com/rankreactorai/rankreactorai-mcp` into the plugin search, and install ([install from a git URL](https://cursor.com/docs/cli/changelog)). A Cursor Marketplace listing is coming soon.
2. In the RankReactor dashboard, open **API Key** and choose **Generate**. Copy the key now; RankReactor shows the full value once.
3. In Cursor, open **Plugins → Configure** and paste the key as `RANKREACTOR_API_KEY`.
4. Reload Cursor (**Developer: Reload Window**) or restart so the MCP server picks up the key.

The plugin sends `Authorization: Bearer ${RANKREACTOR_API_KEY}` to `https://www.rankreactor.ai/mcp`. Cursor substitutes the value you set under **Plugins → Configure**. This repo never stores the secret.

Privacy: [Privacy policy](https://www.rankreactor.ai/privacy-policy). Terms: [Terms of service](https://www.rankreactor.ai/tos).

**Manual MCP config** (without the plugin) in `.cursor/mcp.json` or `~/.cursor/mcp.json` ([Cursor MCP docs](https://cursor.com/docs/mcp)). Use a placeholder or Cursor's secret prompt; do not commit a live key:

```json
{
  "mcpServers": {
    "rankreactor": {
      "url": "https://www.rankreactor.ai/mcp",
      "headers": {
        "Authorization": "Bearer <your-project-key>"
      }
    }
  }
}
```

Do not configure both the plugin-owned server and this manual entry unless you want two connections.

This repo's Cursor plugin files are `.cursor-plugin/plugin.json` and `mcp.json` (for [Cursor Marketplace](https://cursor.com/marketplace/publish) review). A separate root `.mcp.json` is for [cursor.directory](https://cursor.directory) auto-detect and is unchanged.

## Gemini CLI

From [Gemini CLI extensions](https://geminicli.com/docs/extensions) and the [extension reference](https://geminicli.com/docs/extensions/reference/):

```bash
gemini extensions install https://github.com/rankreactorai/rankreactorai-mcp
```

The root `gemini-extension.json` registers the remote server with `httpUrl`. After install, start a new CLI session. If authentication is needed, run `/mcp auth rankreactor`. When **Connect RankReactorAI** opens, paste the project key and choose **Allow**.

You can also add the same server in `~/.gemini/settings.json` without the extension:

```json
{
  "mcpServers": {
    "rankreactor": {
      "httpUrl": "https://www.rankreactor.ai/mcp"
    }
  }
}
```

Use `httpUrl` (Streamable HTTP). Gemini CLI's `url` field is SSE.

## VS Code (GitHub Copilot)

From [VS Code MCP servers](https://code.visualstudio.com/docs/agent-customization/mcp-servers) and the [MCP configuration reference](https://code.visualstudio.com/docs/agents/reference/mcp-configuration): workspace file `.vscode/mcp.json`, or **MCP: Open User Configuration**. VS Code uses a top-level `servers` object (not `mcpServers`).

```json
{
  "servers": {
    "rankreactor": {
      "type": "http",
      "url": "https://www.rankreactor.ai/mcp"
    }
  }
}
```

Or run **MCP: Add Server** from the Command Palette, choose HTTP, and paste the URL. Start the server. When **Connect RankReactorAI** opens, paste the project key and choose **Allow**. Then open Copilot Chat in Agent mode and enable the RankReactor tools if asked. If OAuth does not run, the same `type: http` entry can take `headers.Authorization` as `Bearer <your-project-key>` (do not commit a live key).

## Perplexity and Le Chat

**Perplexity** ([custom remote connectors](https://www.perplexity.ai/help-center/en/articles/13915507-adding-custom-remote-connectors)): add a custom connector with MCP server URL `https://www.rankreactor.ai/mcp`, authentication **OAuth**, transport **Streamable HTTP**. When **Connect RankReactorAI** opens, paste the project key and choose **Allow**.

**Mistral Le Chat / Work** ([MCP connectors](https://docs.mistral.ai/vibe/work/connectors/mcp-connectors)): **Connectors → Add Connector → Custom MCP Connector**, name `rankreactor`, server URL `https://www.rankreactor.ai/mcp`. The platform can detect OAuth 2.1 with dynamic client registration. When **Connect RankReactorAI** opens, paste the project key and choose **Allow**.

## What this repo contains

| Path | Purpose |
| --- | --- |
| `.cursor-plugin/plugin.json` | Cursor Marketplace plugin manifest |
| `mcp.json` | Cursor plugin MCP config (discovered by default) |
| `.mcp.json` | cursor.directory + Grok Build MCP config |
| `.grok-plugin/plugin.json` | Grok Build plugin metadata |
| `gemini-extension.json` | Gemini CLI extension manifest (`httpUrl`) |
| `GEMINI.md` | Gemini CLI extension context |
| `server.json` | Official MCP Registry metadata (not published from this repo) |
| `assets/logo.svg` | Logo fetched from [rankreactor.ai/logo.svg](https://www.rankreactor.ai/logo.svg) |
| `LICENSE` | MIT, copyright RankReactorAI |

Client install docs on the product site: [https://www.rankreactor.ai/docs/ai-assistants](https://www.rankreactor.ai/docs/ai-assistants).
