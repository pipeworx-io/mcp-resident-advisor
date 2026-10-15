# @pipeworx/resident-advisor

Resident Advisor (RA) electronic music event listings — look up areas/cities,
browse club nights and festivals by date range and genre, and pull up
artist/DJ and venue/club profiles with their recent and upcoming listed events.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1807+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `ra_search_areas(query, limit?)` — look up RA's numeric area id for a
  city/region/country name (e.g. "London"); also returns event and venue
  counts for that area.
- `ra_event_listings(area_id, date_from, date_to?, genre?, page?, page_size?)`
  — list events in an area over a date range, optionally filtered by genre
  (e.g. "techno", "house", "disco").
- `ra_event(event_id)` — full event detail: times, cost, venue, lineup,
  genres, promoters.
- `ra_artist(name?, artist_id?, event_limit?)` — artist/DJ profile (follower
  count, country, social links) and recent/upcoming listed events; resolves
  a free-text name via RA's own search if no id is given.
- `ra_venue(name?, venue_id?, event_limit?)` — venue/club profile (address,
  capacity, area, top artists) and recent/upcoming listed events; resolves a
  free-text name via RA's own search if no id is given.

## Auth

Keyless. No account, key or payment wall.

## Data sources

- Resident Advisor (RA) — its own public event-listing index covering
  electronic music areas/cities, events, artists/DJs and venues/clubs.
  Publisher-run; this pack calls it live per request (proxy, not a mirror),
  so no separate reuse grant is needed.

## Notes

- Only public listing content is returned — no attendee lists, guest lists
  or any personal contact data.
- `ra_artist` / `ra_venue` resolve a free-text `name` to RA's numeric id via
  RA's own global search index (best relevance match), so passing a direct
  `artist_id` / `venue_id` is optional but skips that extra lookup.
- `ra_event_listings`'s `genre` filter matches RA's own genre taxonomy (e.g.
  "techno", "house", "disco", "drum-and-bass", "breaks") case-insensitively.
- `ra_event_listings` defaults `date_to` to 30 days after `date_from` when
  omitted.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "resident-advisor": {
      "url": "https://gateway.pipeworx.io/resident-advisor/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/resident-advisor/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 1807+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/ra_search_areas \
  -H 'Content-Type: application/json' \
  -d '{"query":"London"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/ra_search_areas`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "resident-advisor": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-resident-advisor"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-resident-advisor
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Resident Advisor data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
