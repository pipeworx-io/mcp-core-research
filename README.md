# @pipeworx/core-research

CORE (core.ac.uk), the Open University's aggregator of open-access research:
search papers across thousands of repositories and journals and fetch one
paper's metadata, abstract and full-text download link by CORE id.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1679+ live data sources.

## Tools

- `search_papers(query, limit?)` — matching works: title, authors, abstract,
  DOI, year, download URL, publisher.
- `get_paper(id)` — one work by CORE id, with language, citation count and
  reference count.

## Auth

BYO only: pass your own CORE API key as `_apiKey`. Register at
<https://core.ac.uk/services/api>. There is no platform key, so the call runs
under the caller's own CORE licence.

## Licence — zero-rated (fleet #1974)

CORE's Terms & Conditions (<https://core.ac.uk/terms>) §3 "Other datasets and
the API": the API needs a licence; individuals and public research
organisations may get a free one provided they *"acknowledge the use of
CORE"*; and anyone *"developing or intending to develop a product, service or
software using CORE data which might be monetised now or in the future"* must
contact CORE. Consequences:

- `zeroRated: true` on the gateway entry: 0 credits on every tier, so the
  gateway is never the monetised product §3 describes.
- Every response leads with `attribution`, `license`
  (`LicenseRef-CORE-Terms-and-Conditions`, `kind: vendor_terms`, obligations
  `attribution` + `non_commercial`) and `license_note`, via `attachLicense`
  in `@pipeworx/shared`.
- The caller's own CORE licence governs what they may do with the data.

## Data sources

- <https://api.core.ac.uk/v3/search/works> — search (note the trailing-slash
  301 if you call it by hand; the pack follows it).
- <https://api.core.ac.uk/v3/works/{id}> — one work.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "core-research": {
      "url": "https://gateway.pipeworx.io/core-research/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/core-research/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1679+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/core_research_search_papers`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "core-research": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-core-research"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-core-research
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Core Research data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
