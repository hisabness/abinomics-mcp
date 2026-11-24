# Abinomics — reference for people and agents

**Abinomics** is a multilingual reference for people and AI agents: plain-language dictionaries of
**economics, finance, politics and genomics**, in **36 language editions**, with more subjects and
textbook-style material to come. Everything is readable on the web at <https://abinomics.com/> and
available to software through a free read-only **HTTP API** and a remote **MCP server**.

- Website: https://abinomics.com/
- MCP endpoint (Streamable HTTP): `https://abinomics.com/mcp`
- HTTP API: `https://abinomics.com/api/v1/` · OpenAPI 3.1: https://abinomics.com/api/v1/openapi.json
- Developer docs: https://abinomics.com/developers/
- For LLMs: https://abinomics.com/llms.txt
- MCP Registry name: `com.abinomics/reference` ([server.json](server.json))

This repository holds the public metadata and client setup for Abinomics. The server itself is hosted;
there is nothing to install or run.

## Quick start (HTTP API)

No key needed for light use.

```bash
# Define a term by name or abbreviation
curl "https://abinomics.com/api/v1/define?term=ETF&lang=en"

# Search
curl "https://abinomics.com/api/v1/search?q=inflation&lang=en&limit=5"

# One entry by slug (static JSON)
curl "https://abinomics.com/api/v1/en/terms/exchange-traded-fund.json"

# Categories in Spanish (economics, finance, politics)
curl "https://abinomics.com/api/v1/es/categories.json"

# Genomics dictionary
curl "https://abinomics.com/api/v1/define?domain=genomics&term=PCR"
curl "https://abinomics.com/api/v1/genomics/en/terms/allele.json"

# With an API key (higher limits)
curl -H "Authorization: Bearer abn_YOUR_KEY" "https://abinomics.com/api/v1/search?q=bond"
```

A Postman collection generated from the OpenAPI spec is in [postman/](postman/).

## MCP setup

The server is public; an API key is optional (send `Authorization: Bearer abn_...` for higher limits).

**Claude Desktop / claude.ai** — Settings → Connectors → *Add custom connector* → URL
`https://abinomics.com/mcp`. Or, in `claude_desktop_config.json`, through the `mcp-remote` bridge:

```json
{
  "mcpServers": {
    "abinomics": { "command": "npx", "args": ["-y", "mcp-remote", "https://abinomics.com/mcp"] }
  }
}
```

**Claude Code**

```bash
claude mcp add --transport http abinomics https://abinomics.com/mcp
# with a key:
claude mcp add --transport http abinomics https://abinomics.com/mcp --header "Authorization: Bearer abn_YOUR_KEY"
```

**Cursor** — `~/.cursor/mcp.json` (or `.cursor/mcp.json` in a project):

```json
{
  "mcpServers": {
    "abinomics": { "url": "https://abinomics.com/mcp" }
  }
}
```

**VS Code (GitHub Copilot agent mode)** — `.vscode/mcp.json`:

```json
{
  "servers": {
    "abinomics": { "type": "http", "url": "https://abinomics.com/mcp" }
  }
}
```

or `code --add-mcp '{"name":"abinomics","type":"http","url":"https://abinomics.com/mcp"}'`

**ChatGPT** — Settings → Apps & Connectors → Advanced settings → enable *Developer mode* → *Create*
connector: name `Abinomics`, MCP server URL `https://abinomics.com/mcp`, authentication *None*.

**Windsurf** — `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "abinomics": { "serverUrl": "https://abinomics.com/mcp" }
  }
}
```

## Tools

| Tool | What it does |
|---|---|
| `define_term` | Get the definition of a finance or economics term by its name or abbreviation (e.g. |
| `search_terms` | Search Abinomics term names, aliases and abbreviations (and, as a last resort, definition text). |
| `get_term` | Fetch one dictionary entry by its slug (the stable id used in URLs, e.g. |
| `list_categories` | List dictionary categories (economics, finance, politics; older keys such as crypto or markets are aliases of finance) with localized labels and term counts. |
| `browse_category` | List terms in one category (use list_categories for keys), alphabetically with short definitions. |
| `list_languages` | List the 36 language editions with codes, names, site URLs and translation coverage. |
| `random_term` | Return a random dictionary entry, e.g. |
| `define_genomics_term` | Get the definition of a genetics, genomics or molecular biology term by name or abbreviation (e.g. |
| `search_genomics` | Search genomics/genetics term names and abbreviations (and, as a last resort, definition text). |
| `get_genomics_term` | Fetch one genomics dictionary entry by its slug (stable id used in /genomics/{slug}/ URLs, e.g. |
| `list_genomics_categories` | List genomics dictionary categories (molecular biology, genomics & sequencing, classical genetics, cytogenetics, population genetics, medical genetics, biotechnology, model organisms, ...) with labels and term counts. |
| `browse_genomics_category` | List genomics terms in one category (use list_genomics_categories for keys), alphabetically with short definitions. |
| `random_genomics_term` | Return a random genomics dictionary entry, optionally within one category, e.g. |
| `get_pricing` | Paid plans and prepaid credit packs with prices and limits (not metered). |
| `create_checkout` | Stripe Checkout link for a plan or credit pack, to hand to a human (not metered). |
| `check_checkout` | Whether a checkout is paid; returns the upgraded key's tier/credits or a newly issued key (not metered). |
| `get_usage` | Your tier, usage today, credits left and reset time (not metered). |

The server also offers prompts and resources; see the [server card](.well-known/server-card.json).

## Languages

English (`en`), Afrikaans (`af`), Arabic (`ar`), Belarusian (`be`), Bulgarian (`bg`), Catalan (`ca`), Chinese (Simplified) (`zh-CN`), Chinese (Traditional) (`zh-TW`), Croatian (`hr`), Czech (`cs`), Danish (`da`), Dutch (`nl`), Filipino (`tl`), Finnish (`fi`), French (`fr`), German (`de`), Greek (`el`), Hebrew (`iw`), Hindi (`hi`), Hungarian (`hu`), Icelandic (`is`), Indonesian (`id`), Irish (`ga`), Italian (`it`), Japanese (`ja`), Korean (`ko`), Norwegian (`no`), Persian (`fa`), Polish (`pl`), Portuguese (`pt`), Romanian (`ro`), Russian (`ru`), Serbian (`sr`), Spanish (`es`), Swedish (`sv`), Thai (`th`).

Non-English entries report `translation.status` (original, curated, machine or english-fallback);
fallback entries are English, not translations.

## Limits, keys and pricing

| Plan | Requests / day | Price |
|---|---|---|
| Anonymous (per IP) | 100 metered requests | Free, no sign-up |
| [Free key](https://abinomics.com/developers/keys/) | 1,000 | Free |
| Developer | 10,000 | $9 / month |
| Pro | 50,000 | $19 / month |
| Business | 500,000 + bulk export | $99 / month |
| Enterprise & AI licensing | Custom | [Contact](https://abinomics.com/developers/pricing/) |
| Prepaid credits (any key) | 1 credit per request after the daily quota | 10k $5 · 100k $29 · 1M $199, never expire |

Static listings (`terms.json`, categories) are unmetered and cacheable. Responses carry
`X-RateLimit-*`, `X-API-Tier` and `X-Credits-Remaining` headers. With the daily quota and
credits used up the API returns **HTTP 402 Payment Required** (MCP: JSON-RPC error -32029 over HTTP 402)
with `error.payment` describing how to pay. Pricing page: https://abinomics.com/developers/pricing/

### Paying from an agent

```bash
# 1. Create a checkout (send your key to upgrade/top up it; omit it to get a new key after payment)
curl -s https://abinomics.com/api/v1/billing/checkout \
  -H "Authorization: Bearer $ABINOMICS_API_KEY" -H 'Content-Type: application/json' \
  -d '{"pack":"credits_100k"}'          # or {"plan":"developer"|"pro"|"business"}
# -> {"checkout_url":"https://checkout.stripe.com/...","session_id":"cs_...","status_url":"..."}

# 2. Give checkout_url to a human (Stripe collects card, billing address, optional business tax ID).
# 3. Poll until paid (returns tier, credits and, if issued, the new api_key):
curl -s "https://abinomics.com/api/v1/billing/session?session_id=cs_..."

# Manage / switch plan / cancel (Stripe Customer Portal link):
curl -s -X POST https://abinomics.com/api/v1/billing/portal -H "Authorization: Bearer $ABINOMICS_API_KEY"
```

Catalog: `GET https://abinomics.com/api/v1/billing/plans`. Billing endpoints and MCP billing tools are never metered.

After the daily quota and prepaid credits are used up, the same 402 also advertises **x402** (USDC on Base, **$0.001 per request**). Retry the request with a `PAYMENT-SIGNATURE` (v2) or `X-PAYMENT` (v1) header. Discovery: `GET https://abinomics.com/api/v1/billing/x402`. Credit packs: `POST https://abinomics.com/api/v1/billing/x402/credits`.

## Citation and license

- Code and configuration snippets in this repository: MIT (see [LICENSE](LICENSE)).
- Dictionary content served by Abinomics is © Abinomics, not open-licensed. Cite "Abinomics" and the
  canonical entry URL returned by the API. Not financial, medical or other professional advice.

## Files

- `server.json` — MCP Registry manifest (`com.abinomics/reference`)
- `openapi.json` — snapshot of https://abinomics.com/api/v1/openapi.json (the live URL is canonical)
- `postman/abinomics.postman_collection.json` — Postman collection
- `.well-known/server-card.json` — static MCP server card (tools, prompts, resources)
- `glama.json.template` — rename to `glama.json` and add the GitHub maintainer username to claim the Glama listing
