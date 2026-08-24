# Pixfaro MCP — image generation MCP server for every image model

One MCP connection, one prepaid balance, every major image model — Nano Banana,
Gemini today, GPT Image next (visible as "coming soon" in `list_models`).
Pixfaro holds the provider accounts, keys, retries and format differences;
your agent just asks for an image.

Works with **Claude Desktop, Claude Code, Cursor, Windsurf**, and any MCP client.

## Quickstart — no terminal needed (≈1 minute)

The remote server is the easiest path: paste one URL, sign in with your
Pixfaro account in the browser window that opens. No API key in any file.

**Claude** (claude.ai / Claude Desktop, any plan): **Settings → Connectors →
Add custom connector** → URL `https://mcp.pixfaro.com/mcp` → sign in.
(Team/Enterprise: an org Owner adds it once under **Organization settings →
Connectors → Custom → Web**.)

**Cursor**: [**Add to Cursor →**](https://cursor.com/install-mcp?name=pixfaro&config=eyJ1cmwiOiJodHRwczovL21jcC5waXhmYXJvLmNvbS9tY3AifQ%3D%3D)
— or **Cursor Settings → MCP → Add new MCP server** (type **URL**, address
`https://mcp.pixfaro.com/mcp`); Cursor runs the OAuth flow itself.

**ChatGPT** (Pro/Plus/Business/Edu): **Settings → enable Developer mode**
(connectors live under **Plugins** since the July 2026 rename, formerly
**Connectors → Advanced**) → add a custom connector with URL
`https://mcp.pixfaro.com/mcp`, OAuth.

## Quickstart — command line / config file

1. Get an API key at [pixfaro.com](https://pixfaro.com) and top up (no subscription).
2. Add the server to your MCP client config:

```json
{
  "mcpServers": {
    "pixfaro": {
      "command": "npx",
      "args": ["-y", "@pixfaro/mcp"],
      "env": { "PIXFARO_KEY": "pf_live_…" }
    }
  }
}
```

3. Ask your agent for an image. That's it.

For Claude Code:

```bash
claude mcp add pixfaro -e PIXFARO_KEY=pf_live_… -- npx -y @pixfaro/mcp
```

Clients that speak streamable HTTP can also take the remote form in the same
file — no key, OAuth on first use:

```json
{ "mcpServers": { "pixfaro": { "url": "https://mcp.pixfaro.com/mcp" } } }
``` Clients
without OAuth can pass an API key instead: `Authorization: Bearer pf_live_…`.

## Tools

| Tool | What it does |
|---|---|
| `generate_image` | prompt → hosted image URL (+cost and balance in the reply) |
| `edit_image` | natural-language edit of a previous generation by its `img_…` id |
| `list_models` | models with price, latency, and what each is best for |
| `render_template` | typeset card (quote/post) from a template — $0.02, ~2 s, sharp type · *remote server; npx parity coming in 0.2* |
| `list_templates` | card templates with slots, style axes and exact prices · *remote server* |
| `upload_asset` | upload a PNG/JPEG (≤5 MB) as a reusable asset (e.g. a card avatar) · *remote server* |
| `get_balance` | current prepaid balance |

Replies carry a hosted URL, never base64 — your agent's context stays small.

Both generation tools accept `aspect_ratio` (e.g. `"16:9"`, `"9:16"`). Edits
keep the source image's shape when you omit it — pass one only to reshape.

## CLI

The same package family ships an unscoped CLI for scripts, CI, and pipelines:

```bash
export PIXFARO_KEY=pf_live_…
npx pixfaro gen "a lighthouse at night, minimal flat style" -a 16:9 -o cover.png
npx pixfaro models
npx pixfaro balance
npx pixfaro edit img_8f2a… "make the sky darker" -o v2.png
```

## Environment

| Variable | Meaning |
|---|---|
| `PIXFARO_KEY` | your API key (`pf_live_…`) — required for everything except `models` |
| `PIXFARO_API_URL` | endpoint override (default `https://api.pixfaro.com`) |

## What this repo is

The thin open client over the Pixfaro API: the MCP server (`@pixfaro/mcp`) and
the `pixfaro` CLI share one HTTP client. The balance/provider layer lives
behind [api.pixfaro.com](https://pixfaro.com) — this code is intentionally
small enough to read before you hand it a key.

MIT © Pixfaro
