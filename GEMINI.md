# RankReactor

RankReactor is a hosted SEO product at [https://www.rankreactor.ai](https://www.rankreactor.ai). This Gemini CLI extension connects to the remote MCP server at `https://www.rankreactor.ai/mcp` (always use the `www` host). There is no local server process. The only network endpoint is that HTTPS URL.

## When to use it

Use RankReactor tools when the user wants to work with a RankReactor project: read site status and stored SEO metrics, list or queue SEO articles, or list or queue UGC-style videos.

## Auth

Every request needs a RankReactor project API key as `Authorization: Bearer`. The user creates or selects a project, generates the key under Dashboard → API Key (shown once as `rr_live_...`), and supplies it through Gemini CLI's native secret input.

This extension declares a sensitive setting `RANKREACTOR_API_KEY`. On install, Gemini CLI prompts for the key, stores it in the keychain, and allowlists that env var. `gemini-extension.json` then sends `Authorization: Bearer ${RANKREACTOR_API_KEY}` to `https://www.rankreactor.ai/mcp`. Without the extension, the same header belongs in `~/.gemini/settings.json` using `$RANKREACTOR_API_KEY` or `${RANKREACTOR_API_KEY}` (Gemini CLI's documented expansion; not `${env:NAME}`).

This Gemini CLI extension uses that API-key header, not `/mcp auth`. ChatGPT custom MCP plugins cannot present a custom API key and must use the hosted OAuth flow instead; do not copy that ChatGPT path into Gemini CLI. If the key is missing here, ask the user to run `gemini extensions config` for the RankReactor API Key setting (or export `RANKREACTOR_API_KEY`) rather than inventing another site.

## How to help

- Stay on the connected RankReactor project. Do not invent another site or guess a domain the user did not provide.
- Prefer read tools first (status, metrics, existing articles or videos) before write tools.
- Write actions (queue an article, refresh a metric, queue a UGC video) need the user's confirmation.
- Article generation on Free is one lifetime article per project, shared with the RankReactor dashboard. Ongoing generation is on paid plans. UGC video tools need Pro or Ultra.
- If a tool is refused, explain the plan or quota message from the server instead of retrying blindly.

## Public product docs

Client-specific install steps and the current hosted-tool list live at [https://www.rankreactor.ai/docs/ai-assistants](https://www.rankreactor.ai/docs/ai-assistants). Privacy: [https://www.rankreactor.ai/privacy-policy](https://www.rankreactor.ai/privacy-policy). Terms: [https://www.rankreactor.ai/tos](https://www.rankreactor.ai/tos). Support: support@rankreactor.ai.
