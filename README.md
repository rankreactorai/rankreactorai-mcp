# RankReactor MCP

Config-only listing package for RankReactor's hosted [Model Context Protocol](https://modelcontextprotocol.io) server. This repository does not contain application or backend code.

[RankReactor](https://www.rankreactor.ai) is a set-and-forget SEO product. You add a live website as a project. RankReactor reads the site, then can generate SEO articles and (on higher plans) UGC-style videos, and it tracks keywords, analytics, technical SEO, and a site audit.

**MCP URL (use this exact host):** `https://www.rankreactor.ai/mcp`

Always use `www`. The bare domain redirects and is not the MCP endpoint.

| | |
| --- | --- |
| Transport | Streamable HTTP |
| Auth | OAuth 2.1 in the browser (dynamic client registration and CIMD, PKCE S256). No API key belongs in client config. |
| Account | A RankReactor account is required. A [Free](https://www.rankreactor.ai) plan is available. |
| Privacy | [Privacy policy](https://www.rankreactor.ai/privacy-policy) |
| Terms | [Terms of service](https://www.rankreactor.ai/tos) |
| Support | [support@rankreactor.ai](mailto:support@rankreactor.ai) |

Plans: **Free** (1 lifetime article per project), **Starter** $39/mo, **Pro** $79/mo, **Ultra** $159/mo. UGC video is available on Pro and Ultra.

The hosted tools let an assistant read project status and stored SEO metrics, list generated articles and videos, refresh metrics, and queue new articles or UGC videos (write actions ask for confirmation). The current tool list is documented at [Use RankReactor with your AI assistant](https://www.rankreactor.ai/docs/ai-assistants).

## Sign-in

Add the URL in your client. The client opens a browser so you can sign in to RankReactor and authorize the connection. Config files in this repo only contain the public URL.

## Claude (claude.ai custom connector)

Remote MCP custom connectors work on Claude, Cowork, and Claude Desktop ([Anthropic help](https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp)). Free Claude accounts can add one custom connector.

**One-click:** [Add RankReactor in Claude](https://claude.ai/customize/connectors?modal=add-custom-connector&connectorName=RankReactor&connectorUrl=https%3A%2F%2Fwww.rankreactor.ai%2Fmcp)

**Pro / Max**

1. Open **Customize → Connectors**.
2. Click **+**, then **Add custom connector**.
3. URL: `https://www.rankreactor.ai/mcp`
4. Leave Advanced settings empty unless Claude asks for a client id.
5. Click **Add**, then finish the browser sign-in.

**Team / Enterprise:** an Owner adds the URL under **Organization settings → Connectors** (**Add → Custom → Web**). Members then open **Customize → Connectors** and click **Connect**.

Enable the connector per conversation from **+ → Connectors**.

## Claude Code

From [Claude Code MCP docs](https://code.claude.com/docs/en/mcp):

```bash
claude mcp add --transport http rankreactor https://www.rankreactor.ai/mcp
```

That writes a local-scope entry. Use `--scope user` for every project, or `--scope project` to write `.mcp.json` in the repo.

Then run `claude`, type `/mcp`, select `rankreactor`, and **Authenticate** so the browser OAuth flow can complete.

## Grok (grok.com)

From [xAI Connectors](https://docs.x.ai/grok/connectors):

1. Open [grok.com/connectors](https://grok.com/connectors).
2. Click **New Connector**, then **Custom**.
3. Enter `https://www.rankreactor.ai/mcp` and finish any authentication prompt.

On Grok Business / Enterprise, a team admin may need to provision the connector first in [console.x.ai](https://console.x.ai) (**Grok Business → Connectors → Add Connector → Other**).

A Grok Build marketplace listing is a separate follow-up (see the pull request for the catalog entry text). You can still point Grok Build at this repo once it is listed, or add the same URL as a custom connector.

## Cursor

**Install link** (opens Cursor; [MCP install links](https://cursor.com/docs/mcp/install-links)):

[Add RankReactor in Cursor](https://cursor.com/en/install-mcp?name=rankreactor&config=eyJ1cmwiOiJodHRwczovL3d3dy5yYW5rcmVhY3Rvci5haS9tY3AifQ%3D%3D)

Deeplink equivalent:

```text
cursor://anysphere.cursor-deeplink/mcp/install?name=rankreactor&config=eyJ1cmwiOiJodHRwczovL3d3dy5yYW5rcmVhY3Rvci5haS9tY3AifQ==
```

**Manual `mcp.json`** (Cursor [MCP docs](https://cursor.com/docs/mcp)): project file `.cursor/mcp.json`, or `~/.cursor/mcp.json` for every project.

```json
{
  "mcpServers": {
    "rankreactor": {
      "url": "https://www.rankreactor.ai/mcp"
    }
  }
}
```

This repo also ships a Cursor plugin manifest at `.cursor-plugin/plugin.json` plus `mcp.json` for [Cursor Marketplace](https://cursor.com/marketplace/publish) review, and a root `.mcp.json` for [cursor.directory](https://cursor.directory) auto-detect.

## Gemini CLI

From [Gemini CLI extensions](https://geminicli.com/docs/extensions) and the [extension reference](https://geminicli.com/docs/extensions/reference/):

```bash
gemini extensions install https://github.com/rankreactorai/rankreactorai-mcp
```

The root `gemini-extension.json` registers the remote server with `httpUrl`. After install, start a new CLI session. If the server needs sign-in, run `/mcp auth rankreactor` and finish in the browser.

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

Or run **MCP: Add Server** from the Command Palette, choose HTTP, and paste the URL. Start the server, open Copilot Chat in Agent mode, and enable the RankReactor tools if asked.

## Perplexity and Le Chat

**Perplexity** ([custom remote connectors](https://www.perplexity.ai/help-center/en/articles/13915507-adding-custom-remote-connectors)): add a custom connector with MCP server URL `https://www.rankreactor.ai/mcp`, authentication **OAuth**, transport **Streamable HTTP**.

**Mistral Le Chat / Work** ([MCP connectors](https://docs.mistral.ai/vibe/work/connectors/mcp-connectors)): **Connectors → Add Connector → Custom MCP Connector**, name `rankreactor`, server URL `https://www.rankreactor.ai/mcp`. The platform can detect OAuth 2.1 with dynamic client registration.

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
