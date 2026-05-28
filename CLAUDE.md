# Verbatim Plugin

You have access to the Verbatim platform API for searching specialized document catalogs and getting verbatim cited answers. The launch catalog is the **ACL Anthology** (90,000+ NLP/CL papers); other catalogs may be added over time. Use these endpoints whenever the user asks for academic papers, citations, BibTeX, or research-grounded answers.

**API Key:** Use `$VERBATIM_API_KEY` environment variable. All requests need `Authorization: Bearer $VERBATIM_API_KEY`.

**Base URL:** `https://verbatim.krlabs.eu`

**Catalog scoping:** every action endpoint accepts an optional `catalog_ids` parameter. Default is `["anthology"]` when omitted. On GETs it's a repeated query param (`?catalog_ids=a&catalog_ids=b`); on POSTs it's a JSON array in the body.

## Available Endpoints

| Action | Method | Endpoint | Quota |
|--------|--------|----------|-------|
| List catalogs | GET | `/api/v1/catalogs` | Free |
| Get catalog detail | GET | `/api/v1/catalogs/{catalog_id}` | Free |
| Ask research question (RAG) | POST | `/api/v1/query` | Counts |
| Verbatim Transform (any context) | POST | `/api/v1/transform/verbatim` | Counts |
| Search papers | GET | `/api/v1/papers/search?query=...&limit=10&year=2023&catalog_ids=anthology` | Free |
| Paper metadata | GET | `/api/v1/papers/{id}?catalog_ids=anthology` | Free |
| Paper full text | GET | `/api/v1/papers/{id}/content?catalog_ids=anthology` | Free |
| BibTeX citation | GET | `/api/v1/papers/{id}/bibtex?catalog_ids=anthology` | Free |
| Browse facets | GET | `/api/v1/facets?field=author|venue|booktitle|year&q=...&catalog_ids=anthology` | Free |

## Typical Workflows

- **Research question:** Use `/api/v1/query` with `{"question": "...", "hybrid_weights": {"full_text": 0.5, "dense": 0.5}}`. Add `"filter": "metadata[\"year\"] >= 2022"` to scope by year, or `"catalog_ids": ["a", "b"]` for cross-catalog.
- **Find papers then get bibtex:** Search with `/api/v1/papers/search`, then call `/api/v1/papers/{id}/bibtex` for each result.
- **Get full paper text:** `/api/v1/papers/{id}/content`.
- **Verbatim Transform (general):** POST to `/api/v1/transform/verbatim` with `{"question": "...", "context": [{"content": "...", "title": "..."}]}`. Catalog-agnostic — pass your own documents.

## Notes

- RAG queries and Transform count towards quota (50 free, 1000 pro). Search/browse are free.
- Use `curl -s` and pipe through `jq` for clean output.
- Paper IDs come from search results or RAG query responses.
- Omitting `catalog_ids` is equivalent to passing `["anthology"]`.
