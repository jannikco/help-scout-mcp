# Help Scout MCP server

Find active support conversations and inspect one selected conversation in detail.

A direct-provider MCP server with useful local execution. No PipMCP account is required for local use. Provider API access and any provider charges are your responsibility.

## Quick start

Requires Node.js22+. Clone this repository, then run:

```sh
npm ci --ignore-scripts
npm test
npm start
```

The default command inspects the tools offline. Configure your own credentials in your environment:

- `HELP_SCOUT_ACCESS_TOKEN`



Credential help: https://developer.helpscout.com/mailbox-api/endpoints/conversations/list/

## Provider setup

Supply your own Help Scout OAuth access token from your app; local manual token mode is supported, no browser-login helper is claimed. Help Scout plan and user permissions apply. This manifest is read-only; note creation empty201 receipts are unsupported by the current JSON runtime.

## Supported scope

- Read-only list/detail tools. Note creation is omitted because its empty HTTP201 response requires a future runtime improvement. Merged conversation301 redirects fail closed; obtain a current authorized conversation ID.

## Execute a real read

Replace example record IDs with records you are authorized to access.

```sh
npm start -- --call helpscout_list_conversations '{"page":1}'
```

## Use with an MCP client

Launch `node /absolute/path/help-scout-mcp/server.mjs` using stdio transport; pass credentials through the client environment. Stdout is reserved for JSON-RPC.

## Tools

| Tool | Effect | Purpose |
| --- | --- | --- |
| helpscout_list_conversations | Read | Read one page of active conversations, optionally filtered by mailbox. |
| helpscout_get_conversation | Read | Read one conversation by ID. Merged-conversation redirects are rejected rather than followed. |

## Concrete workflow

Find active support conversations and inspect one selected conversation in detail.

1. helpscout_list_conversations
2. helpscout_get_conversation

## Optional writes

Writes are disabled by default. Set CONNECTOR_ALLOW_WRITES=true and supply `_approval: {approved:true,idempotencyKey:"unique-request-id"}` on the specific call. Do not reuse a key for a different request. Uncertain outcomes are never automatically replayed; inspect the provider before another attempt.

## Capability evidence

Local: **Tested — HTTP fixtures and real stdio tools/call**. Managed: **Documented**. Provider authentication and live operations have not been established by these fixture tests. Evidence applies per operation and execution mode; setup discovery does not imply provider execution.

## Managed production

[Import with PipMCP](https://pipmcp.com/github/start?integration=openapi-to-mcp&utm_source=github&utm_campaign=github-help-scout-mcp&utm_content=readme) when you want the hosted service. This provider is not currently listed as a managed template. Import selected-openapi.json (REST) or the official GraphQL schema linked in provenance, configure authentication, and review the tools before execution. Local credentials are not uploaded by this code. Review provider permissions in the managed onboarding. [Directory and related projects](https://jannikco.github.io/pipmcp-connectors/integrations/help-scout/).

## Provenance and maintenance

- https://developer.helpscout.com/mailbox-api/endpoints/conversations/list/
- https://developer.helpscout.com/mailbox-api/endpoints/conversations/get/

Maintainer:jannikco. MIT license for this code. See connector.json for selected operations and integration.json for evidence. Runtime dependencies retain their own licenses. Report sensitive issues through https://pipmcp.com/contact; do not post credentials or customer data in public issues.
