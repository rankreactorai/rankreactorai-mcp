# RankReactor

RankReactor is a hosted SEO product at [https://www.rankreactor.ai](https://www.rankreactor.ai). This Gemini CLI extension connects to the remote MCP server at `https://www.rankreactor.ai/mcp` (always use the `www` host). There is no local server process. The extension config has no API key; you still generate a project API key in the RankReactor dashboard.

## When to use it

Use RankReactor tools when the user wants to work with a RankReactor project: read site status and stored SEO metrics, list or queue SEO articles, or list or queue UGC-style videos.

## Auth

The server uses OAuth 2.1. Unauthenticated calls return 401. Gemini CLI discovers auth from the server metadata and opens **Connect RankReactorAI**. The user must have a RankReactor account (a Free plan is available), create or select a project, generate that project's API key under Dashboard → API Key, paste the key on the Connect page, and choose Allow. That page is not a Google, magic-link, or other RankReactor account sign-in. OAuth keeps the key out of `gemini-extension.json`; it does not skip key generation. If `/mcp auth` is needed, authenticate the `rankreactor` server and finish by pasting the project key. Clients that cannot complete OAuth may send the same key as `Authorization: Bearer` on the hosted HTTP URL.

## How to help

- Stay on the connected RankReactor project. Do not invent another site or guess a domain the user did not provide.
- Prefer read tools first (status, metrics, existing articles or videos) before write tools.
- Write actions (queue an article, refresh a metric, queue a UGC video) need the user's confirmation.
- Article generation on Free is one lifetime article per project, shared with the RankReactor dashboard. Ongoing generation is on paid plans. UGC video tools need Pro or Ultra.
- If a tool is refused, explain the plan or quota message from the server instead of retrying blindly.

## Public product docs

Client-specific install steps and the current hosted-tool list live at [https://www.rankreactor.ai/docs/ai-assistants](https://www.rankreactor.ai/docs/ai-assistants). Privacy: [https://www.rankreactor.ai/privacy-policy](https://www.rankreactor.ai/privacy-policy). Terms: [https://www.rankreactor.ai/tos](https://www.rankreactor.ai/tos). Support: support@rankreactor.ai.
