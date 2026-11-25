# WEIR AI MCP server

**Likeness clearance and identity license terms for a named person. A listing is never permission.**

WEIR AI is an identity rights platform: people register their name, image and likeness (NIL) and publish a license that says how others may use it. The WEIR AI MCP server exposes the public API as Model Context Protocol tools, so any MCP-aware agent (Claude, Cursor, ChatGPT, Lovable and others) can check whether a named person has declared how their likeness may be used, search the public license catalog, read license terms and look for possible name mentions, without glue code.

This repository holds documentation and registry metadata only. The server is hosted by WEIR AI and is not open source.

| | |
|---|---|
| Endpoint | `https://wapi.weir.ai/mcp` |
| Transport | Streamable HTTP (JSON-RPC 2.0) |
| Protocol | `2026-07-28` (`2025-03-26` also accepted) |
| Registry name | `ai.weir/weir-ai` ([official MCP registry](https://registry.modelcontextprotocol.io/v0/servers?search=ai.weir)) |
| Manifest | <https://weir.ai/.well-known/mcp.json> |
| Docs | <https://weir.ai/developers/mcp> |

## A listing is never permission

- A catalog listing, a missing listing, a missing field or `no_public_declaration` is never permission to use someone's identity.
- Rights come only from the license terms plus a completed WEIR AI licensing step. Share `terms_url` so the person can review the terms on WEIR AI.
- Mention search returns candidate matches, not verified identification or evidence of infringement.
- Purchases, payments, identity verification, biometric consent, payouts, claims and takedown submissions are done by a person on [weir.ai](https://weir.ai), never by an agent.

## Example check

```text
You:   Using WEIR AI, can I use Albert Einstein's likeness in an ad?
Tool:  check_identity({ "name": "Albert Einstein" })
       → { "status": "open", "terms_url": "https://weir.ai/licenses/WR6SN6X0F", "verification_status": "self_declared" }
```

`status` is one of `blocked`, `protect`, `earn`, `open` or `no_public_declaration`. None of them is permission by itself: read the terms at `terms_url`. Results are served live and change at any time.

The same check works without MCP or sign-in:

```bash
curl "https://wapi.weir.ai/metadata/check?name=Albert%20Einstein"
```

## Connect

Seven tools are public and need no account. For a member's own licenses and mentions, assistants connect with OAuth: the member signs in and approves read-only access. Never ask a member for keys, codes or passwords.

### Claude Desktop

Add WEIR AI as a custom connector (this also works on claude.ai):

1. Open **Settings → Connectors** and choose **Add custom connector**.
2. Name it `WEIR AI` and enter the URL `https://wapi.weir.ai/mcp`.
3. Click **Add**. To reach your own licenses and mentions, click **Connect** and sign in to WEIR AI to approve read-only access.

The seven public tools need no WEIR AI account. No API key or `claude_desktop_config.json` entry is needed.

### Cursor

In **Settings → MCP → Add new MCP server**:

```text
Name: WEIR AI
Type: HTTP
URL:  https://wapi.weir.ai/mcp
Headers:
  Authorization: Bearer YOUR_WEIR_TOKEN
```

### ChatGPT

With developer mode turned on, go to **Settings → Apps & Connectors → Create** and enter the endpoint above. Choose **OAuth** to sign in and approve read-only access to your licenses and mentions, or **No authentication** for the seven public tools. ChatGPT does not accept a fixed Bearer header; developer API keys are not needed for assistant connections.

Things to try:

- "Using WEIR AI, find earn licenses I could use for an ad campaign."
- "What does WEIR AI license WRT3E0FAY allow?"
- "What's the difference between protect and blocked on WEIR AI?"

### Test it

```bash
npx @modelcontextprotocol/inspector
```

Then point it at `https://wapi.weir.ai/mcp`.

### Developer tokens

The Bearer header in the Cursor snippet above is for developer integrations only, and is optional: leave it out to use the public tools. Tokens come from `POST https://wapi.weir.ai/auth/token` with your API key and secret, the same flow as the REST API. See [Authentication](https://weir.ai/developers/mcp).

## Tools

| Tool | Auth | Description |
|---|---|---|
| `health` | public | Service health and version. |
| `discovery` | public | Returns `.well-known/weir`. |
| `search_catalog` | public | Browse the public license catalog. |
| `license_types` | public | Available license types. |
| `get_public_license` | public | One public catalog license. |
| `check_identity` | public | Start here to check whether a named person's likeness can be used; no declaration is never permission. |
| `search_mentions` | public | Possible name mentions; connected results follow the member's plan. |
| `list_licenses` | bearer | Licenses you own or have access to. |
| `get_license` | bearer | Single license by `external_id`. |
| `list_license_mentions` | bearer | Detected mentions for a license. |
| `list_license_images` | developer only | Reference images; unavailable to assistant connections. |
| `list_bookmarks` | developer only | Saved mention bookmarks. |
| `create_bookmark` | bearer · change | Bookmark a mention. |
| `list_webhooks` | bearer | List webhook subscriptions. |
| `create_webhook` | bearer · change | Subscribe to events. |
| `delete_webhook` | bearer · change | Remove a webhook. |

An unauthenticated `tools/list` returns the first ten tools (the seven public ones plus `list_licenses`, `get_license` and `list_license_mentions`, which need a connected account to call). The remaining six belong to the developer catalog.

Tools marked **change** modify a member's data. Run them only on an explicit instruction from the member who owns the key. Assistant connections are read-only.

Rate limit: 100 requests/hour per key (10 in development mode). On `RATE_LIMITED` (429), wait for `Retry-After`.

## License types

| Type | Meaning |
|---|---|
| `open` | Free to use, often with attribution. Follow the terms. |
| `earn` | Paid commercial license; the person reviews and completes it on WEIR AI. |
| `protect` | Actively monitored; commercial use is discouraged. |
| `blocked` | No use permitted. |

Call `license_types` for the current definitions.

## Links

- MCP server docs: <https://weir.ai/developers/mcp>
- Ways to integrate (MCP, REST, discovery): <https://weir.ai/developers/integrations>
- Agent skill: <https://weir.ai/skills/weir-ai/SKILL.md>
- Guide for AI agents: <https://weir.ai/ai-agents.md>
- LLM documentation: <https://weir.ai/llms.txt> · <https://weir.ai/llms-full.txt>
- A2A messaging: <https://weir.ai/developers/a2a>
- API reference (OpenAPI): <https://weir.ai/openapi-external-v2.json>
- Terms: <https://weir.ai/terms> · Privacy: <https://weir.ai/privacy>
- Support: <support@weir.ai> · Security: see [SECURITY.md](SECURITY.md)

## License

The files in this repository are released under the [MIT License](LICENSE). That license covers this repository's documentation and metadata only. It does not cover the WEIR AI service, its API or MCP server, or any member data or identity license returned by them; those are governed by the [WEIR AI Terms](https://weir.ai/terms) and by each license's own terms.
