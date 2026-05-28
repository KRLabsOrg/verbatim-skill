# Verbatim Plugin

You have access to the Verbatim platform API for searching specialized document collections and getting verbatim cited answers. The launch collection is the **ACL Anthology** (90,000+ NLP/CL papers); other collections may be added over time. Use these endpoints whenever the user asks for academic papers, citations, BibTeX, or research-grounded answers.

**API Key:** Use `$VERBATIM_API_KEY` environment variable. All requests need `Authorization: Bearer $VERBATIM_API_KEY`.

**Base URL:** `https://verbatim.krlabs.eu`

**Collection scoping:** every action endpoint accepts an optional `collection_ids` parameter. Default is `["anthology"]` when omitted. On GETs it's a repeated query param (`?collection_ids=a&collection_ids=b`); on POSTs it's a JSON array in the body.

## Available Endpoints

| Action | Method | Endpoint | Quota |
|--------|--------|----------|-------|
| List collections | GET | `/api/v1/collections` | Free |
| Get collection detail | GET | `/api/v1/collections/{collection_id}` | Free |
| Ask research question (RAG) | POST | `/api/v1/query` | Counts |
| Verbatim Transform (any context) | POST | `/api/v1/transform/verbatim` | Counts |
| Search papers | GET | `/api/v1/papers/search?query=...&limit=10&year=2023&collection_ids=anthology` | Free |
| Paper metadata | GET | `/api/v1/papers/{id}?collection_ids=anthology` | Free |
| Paper full text | GET | `/api/v1/papers/{id}/content?collection_ids=anthology` | Free |
| BibTeX citation | GET | `/api/v1/papers/{id}/bibtex?collection_ids=anthology` | Free |
| Browse facets | GET | `/api/v1/facets?field=author|venue|booktitle|year&q=...&collection_ids=anthology` | Free |

## Typical Workflows

- **Research question:** Use `/api/v1/query` with `{"question": "...", "hybrid_weights": {"full_text": 0.5, "dense": 0.5}}`. Add `"filter": "metadata[\"year\"] >= 2022"` to scope by year, or `"collection_ids": ["a", "b"]` for cross-collection.
- **Find papers then get bibtex:** Search with `/api/v1/papers/search`, then call `/api/v1/papers/{id}/bibtex` for each result.
- **Get full paper text:** `/api/v1/papers/{id}/content`.
- **Verbatim Transform (general):** POST to `/api/v1/transform/verbatim` with `{"question": "...", "context": [{"content": "...", "title": "..."}]}`. Collection-agnostic — pass your own documents.

## Notes

- RAG queries and Transform count towards quota (50 free, 1000 pro). Search/browse are free.
- Use `curl -s` and pipe through `jq` for clean output.
- Paper IDs come from search results or RAG query responses.
- Omitting `collection_ids` is equivalent to passing `["anthology"]`.
