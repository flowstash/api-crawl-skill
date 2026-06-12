---
name: api-crawl
description: "Use when searching or exploring API documentation indexed in the api-crawl catalog via REST. Use for: find endpoint, search API docs, lookup section content, query api-crawl catalog, explore ingested API, fetch endpoint detail, fetch section detail."
argument-hint: "Describe what you want to find, e.g. 'search Stripe docs for authentication'"
---

# api-crawl REST Skill

Query the api-crawl catalog via its REST API — the same data exposed by the MCP tools
(`search_api_docs`, `search_api_catalog`) but using plain HTTP calls.

**Base URL**: `https://api.apicrawl.dev`  
**Key source files**: [catalog.py](../../../src/api_server/routers/catalog.py), [search.py](../../../src/api_server/routers/search.py)

---

## Workflow

Follow these steps in order. Skip steps where the information is already known.

### Step 1 — Find the `catalog_id` / api id, if you dont have it

If you already know the catalog_id, skip to Step 2.
If you don't have the API Id, we need to search it first.

List all ingested APIs, optionally filtered by keyword:

```bash
# List everything
curl -s "https://api.apicrawl.dev/catalog"

# Search by keyword
curl -s "https://api.apicrawl.dev/catalog?q=stripe"
```

**Response fields**: `catalog_id`, `name`, `domain`, `doc_format`, `page_count`, `ingest_status`

Pick the `catalog_id` of the API you want to explore.

---

### Step 2 — Search docs (LLM-friendly mode)

Use `mode=llm` to get compact, LLM-optimized results. Returns `title`, `snippet`, `url`,
`section_id`, and `endpoint_id` — use these ids to fetch full detail in Step 3/4.

```bash
# Catalog-scoped GET search (LLM-friendly)
# Note: the `search` router is mounted at the application root — the correct
# path for a catalog-scoped search is `/{catalog_id}/search` (no `/catalog` prefix).
curl -s "https://api.apicrawl.dev/{catalog_id}/search?q=authentication&mode=llm&limit=10"
```

**Parameters**:
q: search query string (full-text)
mode: always set to `llm` (compact output for LLM consumption)
limit: max number of results (1–20)


**LLM-mode response shape** (`SearchResultLlmFriendlyResponse`):
```json
[
  {
    "title": "Authentication — API Keys",
    "url": "https://docs.example.com/auth",
    "snippet": "Pass your API key in the Authorization header...",
    "section_id": "authentication-api-keys",
    "endpoint_id": null
  }
]
```

> `section_id` is set for documentation sections; `endpoint_id` for API endpoints.
> Both may be null if the result maps to neither.


## Setting up client
If client config is not yet set up (usually a file in `config/shared/clients/` folder) you should ask whether to create it. The common pattern is to create config named `<clientId>.yaml` and declare the config:
```yaml
# apiCrawl api id: `[catalog_id]`
baseUrl: ?
auth: 
  [authInfo]
retry:
  max_retries: ?
```

$authInfo can info can be usually found in ApiCrawl endpoint `/catalog/{catalog_id}/auth`

```sh
curl -s "https://api.apicrawl.dev/catalog/{catalog_id}/auth?mode=llm"
```

## Tips

- When searching for catalog, you can search also by domain or docs url... especially if you know the domain url, you get better match... if unsure, search by keyword and review the results

- Search docs by using list of 'q' args to get better match...

You can pass `q` multiple times to provide alternative queries or synonyms. The
GET endpoint accepts repeated `q` params (`list[str]`) and this will prioritize results that match more of the provided keywords.

```bash
curl -s "https://api.apicrawl.dev/api-aims360-io/search?q=authentication&q=api+key&mode=llm&limit=5"
```

