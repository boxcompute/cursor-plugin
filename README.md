# BoxCompute plugin for Cursor

Create and drive [BoxCompute](https://boxcompute.ai) sandbox VMs from Cursor:
run commands, read and edit files, and open public preview URLs, all through
the hosted MCP server at `https://mcp.boxcompute.ai/mcp`.

This repository is the Cursor plugin
([plugin manifest](.cursor-plugin/plugin.json)). It is a thin package over the
hosted MCP server documented at
[boxcompute.ai/docs/mcp](https://boxcompute.ai/docs/mcp/); it contains no
server code.

## What it installs

- One MCP server (`boxcompute`) pointing at `https://mcp.boxcompute.ai/mcp`.
  Transport is Streamable HTTP; the first tool call starts OAuth sign-in.
- One skill, `boxcompute-sandboxes`, that teaches the agent when and how to use
  the sandbox tools.

## Install

Browse the Cursor Marketplace for **BoxCompute** and click **Add to Cursor**
once listed. Until then, add the server directly in `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "boxcompute": {
      "url": "https://mcp.boxcompute.ai/mcp"
    }
  }
}
```

## Authentication

The URL alone signs in through BoxCompute OAuth (the server publishes RFC 9728
protected-resource metadata and supports dynamic client registration, which is
what Cursor uses). To use an API key instead, add a header:

```json
{
  "mcpServers": {
    "boxcompute": {
      "url": "https://mcp.boxcompute.ai/mcp",
      "headers": { "Authorization": "Bearer ${env:BOXCOMPUTE_API_KEY}" }
    }
  }
}
```

Set `BOXCOMPUTE_API_KEY` in the environment Cursor starts from. API keys come
from **Menu → API keys** in the BoxCompute app.

## Testing locally

Clone this repository to `~/.cursor/plugins/local/boxcompute` and reload
Cursor, or point Cursor at the server with the `mcp.json` snippet above.
Verify with any tool call; `account_get` needs only read scope.

## License

[MIT](LICENSE)
