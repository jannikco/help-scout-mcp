# Help Scout conversation research for AI assistants

Find active support conversations and inspect one selected conversation in detail.

[Start locally](#get-started) · [Connect your assistant](#connect-your-assistant) · [Available tools](#available-tools) · [Hosted PipMCP setup](#use-pipmcp-for-production)

## Built for useful workflows

- **Build a Help Scout brief.** Read one page of active conversations, optionally filtered by mailbox. Make that provider response available to your assistant before it proposes a next action.
- **Investigate a chosen Help Scout result.** Read one conversation by ID. Merged-conversation redirects are rejected rather than followed. Use an account or record returned by your provider rather than guessing an identifier.
- **Keep the workflow read-only.** The shipped operations expose Help Scout reads. Combine their returned context in your assistant without adding a provider write operation.

## Why use this package?

Get a focused Help Scout MCP integration without assembling request mappings and a stdio transport yourself. The package provides documented input schemas, provider authentication handling and an example CLI, with the same tools available to your assistant.

The selected tools keep this Help Scout workflow read-only.

## Get started

**Requirements:** Node.js 22 or newer, npm, and access to the provider account you intend to use.

```sh
git clone https://github.com/jannikco/help-scout-mcp.git
cd help-scout-mcp
npm ci --ignore-scripts
npm test
npm start
```

`npm start` shows the tool inventory and workflow offline. It does not authenticate or call the provider. The tests use provider fixtures and stdio transport; they do not modify your account.

### Configure your provider account

The current release connects directly to the provider with **your own credentials**. It does not require a PipMCP key for local execution. Configure the environment of the process or MCP client that runs the server; `.env` files are not loaded automatically.

| Environment variable | What to supply |
| --- | --- |
| `HELP_SCOUT_ACCESS_TOKEN` | Your provider OAuth access token; use the documented login helper where supported. |

Supply your own Help Scout OAuth access token from your app; local manual token mode is supported, no browser-login helper is claimed. Help Scout plan and user permissions apply. This manifest is read-only; note creation empty201 receipts are unsupported by the current JSON runtime.

[Provider credential/reference documentation](https://developer.helpscout.com/mailbox-api/endpoints/conversations/list/). Keep credentials out of commits, issue reports and shared client configuration.

### Run your first provider read

After configuring the environment, run `helpscout_list_conversations`:

```sh
npm start -- --call helpscout_list_conversations '{"page":1}'
```

Replace sample account, record, file and project IDs with values from your authorized account. This command makes a real provider request and returns its result for you to inspect.

## Available tools

| Capability | MCP tool | Effect | Required inputs |
| --- | --- | --- | --- |
| Read one page of active conversations, optionally filtered by mailbox. | `helpscout_list_conversations` | Read | None |
| Read one conversation by ID. Merged-conversation redirects are rejected rather than followed. | `helpscout_get_conversation` | Read | `conversationId` |

Tool schemas and bindings are in [connector.json](./connector.json). These are the selected operations this package ships.

### A practical tool sequence

1. Use `helpscout_list_conversations` — Read one page of active conversations, optionally filtered by mailbox.
2. Use `helpscout_get_conversation` — Read one conversation by ID. Merged-conversation redirects are rejected rather than followed.

## Connect your assistant

Add this stdio server to an MCP-compatible client. Replace the absolute path and credential placeholders. Use your client’s secret/environment controls instead of committing this configuration.

```json
{
  "mcpServers": {
    "help-scout-mcp": {
      "command": "node",
      "args": [
        "/absolute/path/help-scout-mcp/server.mjs"
      ],
      "env": {
        "HELP_SCOUT_ACCESS_TOKEN": "YOUR_HELP_SCOUT_ACCESS_TOKEN"
      }
    }
  }
}
```

`node server.mjs` starts the MCP server; `npm start` runs the example CLI. The server reserves stdout for MCP messages. If the client does not inherit your shell environment, pass the required variables explicitly.

## Read-only scope

The shipped Help Scout tools are read-only. This package does not provide a write command or silently expand the provider permissions.

## Provider-specific limits

- Read-only list/detail tools. Note creation is omitted because its empty HTTP 201 response requires a future runtime improvement. Merged conversation301 redirects fail closed; obtain a current authorized conversation ID.

## Use PipMCP for production

The local package is useful when you want to run these selected Help Scout operations in your own process. PipMCP provides a hosted place to configure provider connections, publish tools, issue endpoint access tokens, apply server-side policy and approvals, and inspect execution/usage records. Provider credentials and hosted execution are configured in that service.

**[Import Help Scout into PipMCP](https://pipmcp.com/github/start?integration=openapi-to-mcp&utm_source=github&utm_campaign=github-help-scout-mcp&utm_content=readme)**

This provider is not listed as a ready managed template in the current availability snapshot (2026-11-08). The link opens the OpenAPI importer; it does not activate the local tools automatically.

Use [selected-openapi.json](./selected-openapi.json) as a starting point for manual import. Configure the account hostname, authentication and provider-specific requirements, then review and publish the resulting tools in PipMCP.

Local credentials are not uploaded by this repository. A hosted endpoint uses its own PipMCP endpoint access token and published tool inventory; the local and hosted credentials are separate.

## Scope and validation

- The installed package has HTTP request fixtures and MCP stdio `tools/call` tests.
- Fixture success checks client behavior; it does not establish provider authentication, paid-plan access or live account execution.
- No live provider execution is claimed for this package by these tests.
- Hosted tool names, schemas, permissions and availability can differ from this local package. A queued or accepted job/message is not proof of completion or delivery. Provider API plans, account rules and limits apply.
- See [integration.json](./integration.json) for operation-scoped evidence and the provider-specific limits above.

## FAQ

**Do I need a Help Scout account?**

You need the provider account, token and permissions required by these operations. Provider charges and API limits remain with that account.

**Does this version require a PipMCP API key?**

This package uses your provider credentials for local execution. To use the hosted service, configure the provider connection in PipMCP and use the endpoint access token issued there.

**Why is a tool missing or a request rejected?**

This repository exposes the selected tools above. Check the required environment variables, provider scopes, account/region requirements and input IDs first. Hosted endpoints have their own published inventory and may exclude tools by role or policy. Do not retry a failed write without checking its outcome.

**Is this an official provider SDK or a hosted-service guarantee?**

This is an independently maintained MCP integration. Its fixtures and source references do not imply provider endorsement, unrestricted API access, or identical hosted behavior.

## Guides and related workflows

- [Help Scout integration guide](https://jannikco.github.io/pipmcp-connectors/integrations/help-scout/)
- [Help Scout workflow and tool sequence](https://jannikco.github.io/pipmcp-connectors/workflows/help-scout/)
- [More integration workflows](https://jannikco.github.io/pipmcp-connectors/workflows/)
- [Typed LeadAdapter API client](https://github.com/jannikco/linkedin-typescript-sdk)
- [LeadAdapter Python API client](https://github.com/jannikco/linkedin-python-sdk)
- [LinkedIn API quickstart](https://github.com/jannikco/linkedin-api)
- [LinkedIn people and post search](https://github.com/jannikco/linkedin-search-api)
- [LinkedIn post engagers](https://github.com/jannikco/linkedin-post-engagers)
- [LeadAdapter LinkedIn MCP server](https://github.com/jannikco/linkedin-mcp)
- [OpenAPI to MCP import starter](https://github.com/jannikco/openapi-to-mcp)
- [Swagger to MCP import starter](https://github.com/jannikco/swagger-to-mcp)
- [Help Scout API and credential reference](https://developer.helpscout.com/mailbox-api/endpoints/conversations/list/)
- [Additional official source 2](https://developer.helpscout.com/mailbox-api/endpoints/conversations/get/)

Maintained by [jannikco](https://github.com/jannikco). MIT for this repository’s code; dependencies retain their licenses. [Report a reproducible issue](https://github.com/jannikco/help-scout-mcp/issues) with redacted input and error details. For credentials or security issues, use [private contact](https://pipmcp.com/contact).
