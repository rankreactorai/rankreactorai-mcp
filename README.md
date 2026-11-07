# RankReactor MCP

Config-only listing package for RankReactor's hosted [Model Context Protocol](https://modelcontextprotocol.io) server. This repository does not contain application or backend code.

[RankReactor](https://www.rankreactor.ai) is a set-and-forget SEO product. You add a live website as a project. RankReactor reads the site, then can generate SEO articles and (on higher plans) UGC-style videos, and it tracks keywords, analytics, technical SEO, and a site audit.

**MCP URL (use this exact host):** `https://www.rankreactor.ai/mcp`

Always use `www`. The bare domain redirects and is not the MCP endpoint. This package talks only to that HTTPS URL. It does not call Firebase, Cloud Run, or other internal backends.

| | |
| --- | --- |
| Transport | Streamable HTTP |
| Auth | Project API key as `Authorization: Bearer`. Generate the key in Dashboard → API Key, then supply it through the client's native secret input. Do not put a live key in a committed file. |
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
3. Open **API Key** in the dashboard sidebar and choose **Generate**. Copy the key now; RankReactor shows the full value once (`rr_live_...`).

Then configure the client with `https://www.rankreactor.ai/mcp` and send `Authorization: Bearer <your-project-key>`. Use that client's documented secret mechanism (plugin variable, environment variable, password prompt, or the connector's API-key field). This listing does **not** use a browser OAuth / **Connect RankReactorAI** step.

Configs in this repo keep the `Bearer ${…}` placeholder only. Do not commit a live key. Regenerating or revoking the key in the dashboard stops existing connections until you update the stored secret.

This repository does not ship a local stdio server.

### Direct API-key support

Interpolation syntax is client-specific. Do not copy `${env:NAME}` into a client that documents `${NAME}`, or `${input:id}` into a client that does not prompt.

| Client | Direct API key | Native secret input |
| --- | --- | --- |
| Cursor Marketplace plugin | Yes | Plugin variable `RANKREACTOR_API_KEY` (Plugins → Configure). Placeholder in `mcp.json` is `${RANKREACTOR_API_KEY}`, not `${env:…}`. |
| Cursor `mcp.json` / install link | Yes | `${env:RANKREACTOR_API_KEY}` in `headers.Authorization`. Set the variable in the environment Cursor inherits at launch. |
| Claude Code | Yes | `--header` plus `${RANKREACTOR_API_KEY}` in `url`/`headers` (single-quote the header so the shell does not expand it). |
| Claude.ai / Claude Desktop custom connector | Yes only if **Request headers** is in the add-connector dialog | **No sign-in**, then header `authorization` = `Bearer <your-project-key>`. If that section is missing, this path is unsupported. |
| Gemini CLI | Yes | Extension `settings` (`envVar` `RANKREACTOR_API_KEY`) or `headers` with `${RANKREACTOR_API_KEY}` / `$RANKREACTOR_API_KEY`. |
| VS Code Copilot (workspace/profile `mcp.json`) | Yes | `inputs` `promptString` + `${input:rankreactor-api-key}`. |
| VS Code / Copilot portable `.mcp.json` | Yes | `${RANKREACTOR_API_KEY}` in header values (`${NAME}`, not `${env:NAME}` and not `${input:…}`). |
| Grok Build | Yes | `grok mcp add --header` or `.mcp.json` `headers` with `${RANKREACTOR_API_KEY}`. |
| grok.com custom connectors | **No** | Form is URL + OAuth authentication. No documented API-key / Bearer field. |
| Perplexity custom remote connector | Yes | Authentication **API Key**, transport **Streamable HTTP**. |
| Mistral Le Chat / Work | Yes if the platform asks for HTTP Bearer | Paste the project key as the Bearer token. If it only starts OAuth, this path is unsupported. |

## Claude (claude.ai custom connector)

Remote MCP custom connectors work on Claude, Cowork, and Claude Desktop ([Anthropic help](https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp)). Static **Request headers** are the direct-key path ([authenticate with request headers](https://claude.com/docs/connectors/custom/add-unlisted)). They are not available on every Claude organization.

**Pro / Max** (and Team / Enterprise when **Request headers** is shown)

1. Open **Customize → Connectors**.
2. Click **+**, then **Add custom connector**.
3. URL: `https://www.rankreactor.ai/mcp`
4. Authentication: **No sign-in**. Leave OAuth client fields empty.
5. **Request headers**: name `authorization`, value `Bearer <your-project-key>` (include the `Bearer ` prefix). Claude stores the value; do not paste it into chat or a committed file.
6. Click **Add**.

If the dialog has no **Request headers** section, this Claude account cannot use direct API-key auth. Do not add the URL alone: that is the OAuth browser path, which this listing does not use.

**Team / Enterprise:** an Owner adds the URL under **Organization settings → Connectors** (**Add → Custom → Web**) with the same **No sign-in** + `authorization` header. Members then open **Customize → Connectors** and click **Connect**.

Enable the connector per conversation from **+ → Connectors**.

## Claude Code

From [Claude Code MCP docs](https://code.claude.com/docs/en/mcp):

```bash
claude mcp add --transport http rankreactor https://www.rankreactor.ai/mcp \
  --header 'Authorization: Bearer ${RANKREACTOR_API_KEY}'
```

Single quotes keep the `${RANKREACTOR_API_KEY}` placeholder in the saved config. Export that variable in the environment Claude Code inherits. Double quotes expand the value at add time and would write the live key into the config file.

That writes a local-scope entry. Use `--scope user` for every project, or `--scope project` to write `.mcp.json` in the repo (still with the placeholder, never a live key).

Equivalent JSON (`type` may be `http` or `streamable-http`):

```json
{
  "mcpServers": {
    "rankreactor": {
      "type": "http",
      "url": "https://www.rankreactor.ai/mcp",
      "headers": {
        "Authorization": "Bearer ${RANKREACTOR_API_KEY}"
      }
    }
  }
}
```

## Grok

**Grok Build** ([remote MCP tools](https://docs.x.ai/developers/tools/remote-mcp) and `grok mcp add`):

```bash
grok mcp add --transport http rankreactor https://www.rankreactor.ai/mcp \
  --header 'Authorization: Bearer ${RANKREACTOR_API_KEY}'
```

This repo's root `.mcp.json` uses the same `${RANKREACTOR_API_KEY}` header placeholder. Set the variable in the environment Grok Build inherits.

From the xAI API, pass the **raw** project key in the MCP tool `authorization` field. xAI sets `Authorization: Bearer` on requests to `https://www.rankreactor.ai/mcp`.

**grok.com custom connectors** ([xAI Connectors](https://docs.x.ai/grok/connectors)): **not supported** for this API-key path. That UI takes a public MCP URL and OAuth authentication. It has no documented field for a static Bearer project key. Do not add the URL there expecting header auth.

A Grok Build marketplace listing is a separate follow-up (see the pull request for the catalog entry text). You can still point Grok Build at this repo once it is listed.

## Cursor

**Marketplace plugin:** this repo's `.cursor-plugin/plugin.json` declares variable `RANKREACTOR_API_KEY`. After install, set it under **Plugins → Configure**. The plugin `mcp.json` uses `${RANKREACTOR_API_KEY}` (Cursor plugin variables, not `${env:…}`).

**Install link** (opens Cursor; [MCP install links](https://cursor.com/docs/mcp/install-links)). This writes a **user** MCP entry, so it uses Cursor config interpolation `${env:RANKREACTOR_API_KEY}`:

[Add RankReactor in Cursor](https://cursor.com/en/install-mcp?name=rankreactor&config=eyJ1cmwiOiJodHRwczovL3d3dy5yYW5rcmVhY3Rvci5haS9tY3AiLCJoZWFkZXJzIjp7IkF1dGhvcml6YXRpb24iOiJCZWFyZXIgJHtlbnY6UkFOS1JFQUNUT1JfQVBJX0tFWX0ifX0=)

Deeplink equivalent:

```text
cursor://anysphere.cursor-deeplink/mcp/install?name=rankreactor&config=eyJ1cmwiOiJodHRwczovL3d3dy5yYW5rcmVhY3Rvci5haS9tY3AiLCJoZWFkZXJzIjp7IkF1dGhvcml6YXRpb24iOiJCZWFyZXIgJHtlbnY6UkFOS1JFQUNUT1JfQVBJX0tFWX0ifX0=
```

Set `RANKREACTOR_API_KEY` in the environment Cursor inherits when it starts (GUI launches often miss variables that exist only in a shell rc file).

**Manual user/project `mcp.json`** (Cursor [MCP docs](https://cursor.com/docs/mcp)): project file `.cursor/mcp.json`, or `~/.cursor/mcp.json` for every project.

```json
{
  "mcpServers": {
    "rankreactor": {
      "url": "https://www.rankreactor.ai/mcp",
      "headers": {
        "Authorization": "Bearer ${env:RANKREACTOR_API_KEY}"
      }
    }
  }
}
```

Do not copy the plugin `mcp.json` into `~/.cursor/mcp.json`. Plugin files use `${RANKREACTOR_API_KEY}`; user Cursor config uses `${env:RANKREACTOR_API_KEY}`.

This repo also ships a Cursor plugin manifest at `.cursor-plugin/plugin.json` plus `mcp.json` for [Cursor Marketplace](https://cursor.com/marketplace/publish) review, and a root `.mcp.json` for [cursor.directory](https://cursor.directory) auto-detect (portable `${RANKREACTOR_API_KEY}` header).

## Gemini CLI

From [Gemini CLI extensions](https://geminicli.com/docs/extensions) and the [extension reference](https://geminicli.com/docs/extensions/reference/):

```bash
gemini extensions install https://github.com/rankreactorai/rankreactorai-mcp
```

The root `gemini-extension.json` registers the remote server with `httpUrl` and a sensitive setting `RANKREACTOR_API_KEY`. On install, Gemini CLI prompts for the project key, stores it in the keychain, and allowlists that env var for the extension. The header is `Authorization: Bearer ${RANKREACTOR_API_KEY}`. Do not run `/mcp auth` for this listing.

You can also add the same server in `~/.gemini/settings.json` without the extension (`$VAR` / `${VAR}` expansion is documented for `settings.json`):

```json
{
  "mcpServers": {
    "rankreactor": {
      "httpUrl": "https://www.rankreactor.ai/mcp",
      "headers": {
        "Authorization": "Bearer ${RANKREACTOR_API_KEY}"
      }
    }
  }
}
```

Use `httpUrl` (Streamable HTTP). Gemini CLI's `url` field is SSE.

```bash
gemini mcp add --transport http rankreactor https://www.rankreactor.ai/mcp \
  --header 'Authorization: Bearer ${RANKREACTOR_API_KEY}'
```

## VS Code (GitHub Copilot)

From [VS Code MCP servers](https://code.visualstudio.com/docs/agent-customization/mcp-servers) and the [MCP configuration reference](https://code.visualstudio.com/docs/agents/reference/mcp-configuration): workspace file `.vscode/mcp.json`, or **MCP: Open User Configuration**. VS Code uses a top-level `servers` object (not `mcpServers`) and `${input:id}` prompts.

```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "rankreactor-api-key",
      "description": "RankReactor project API key",
      "password": true
    }
  ],
  "servers": {
    "rankreactor": {
      "type": "http",
      "url": "https://www.rankreactor.ai/mcp",
      "headers": {
        "Authorization": "Bearer ${input:rankreactor-api-key}"
      }
    }
  }
}
```

VS Code prompts once, masks the value, and stores it. Do not put a live key in the file. Do not add an `oauth` object and do not set `oauth: false` (not a documented VS Code field for skipping discovery).

Portable workspace-root `.mcp.json` (Copilot harness) does **not** support `${input:…}`. Use `${RANKREACTOR_API_KEY}` in the header instead, with the variable set in the host environment:

```json
{
  "mcpServers": {
    "rankreactor": {
      "type": "http",
      "url": "https://www.rankreactor.ai/mcp",
      "headers": {
        "Authorization": "Bearer ${RANKREACTOR_API_KEY}"
      }
    }
  }
}
```

Or run **MCP: Add Server** from the Command Palette, choose HTTP, paste the URL, and set the Authorization header from the input prompt. Start the server, open Copilot Chat in Agent mode, and enable the RankReactor tools if asked.

## Perplexity and Le Chat

**Perplexity** ([custom remote connectors](https://www.perplexity.ai/help-center/en/articles/13915507-adding-custom-remote-connectors)): add a custom connector with MCP server URL `https://www.rankreactor.ai/mcp`, authentication **API Key**, transport **Streamable HTTP**. Paste the project key into Perplexity's API key field (Perplexity stores it). Do not choose OAuth.

**Mistral Le Chat / Work** ([MCP connectors](https://docs.mistral.ai/vibe/work/connectors/mcp-connectors)): **Connectors → Add Connector → Custom MCP Connector**, name `rankreactor`, server URL `https://www.rankreactor.ai/mcp`. If Work asks for an HTTP Bearer token, paste the project key. If it only starts an OAuth browser flow, this API-key path is unsupported on that account.

## What this repo contains

| Path | Purpose |
| --- | --- |
| `.cursor-plugin/plugin.json` | Cursor Marketplace plugin manifest + `RANKREACTOR_API_KEY` variable schema |
| `mcp.json` | Cursor plugin MCP config (`${RANKREACTOR_API_KEY}` plugin variable) |
| `.mcp.json` | cursor.directory + Grok Build MCP config (`${RANKREACTOR_API_KEY}` env placeholder) |
| `.grok-plugin/plugin.json` | Grok Build plugin metadata |
| `gemini-extension.json` | Gemini CLI extension manifest (`httpUrl`, sensitive `RANKREACTOR_API_KEY` setting) |
| `GEMINI.md` | Gemini CLI extension context |
| `server.json` | Official MCP Registry metadata with required secret `Authorization` header (not published from this repo) |
| `assets/logo.svg` | Logo fetched from [rankreactor.ai/logo.svg](https://www.rankreactor.ai/logo.svg) |
| `LICENSE` | MIT, copyright RankReactorAI |

Client install docs on the product site: [https://www.rankreactor.ai/docs/ai-assistants](https://www.rankreactor.ai/docs/ai-assistants).
