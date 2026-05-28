# Verbatim Skill

Search and query specialized document collections with verbatim, cited answers. Defaults to the **ACL Anthology** collection (90,000+ papers). Also includes a general-purpose **Verbatim Transform** that turns any question + context into a cited, verbatim answer.

## Setup

You need a Verbatim API key. Get one at https://verbatim.krlabs.eu (sign up → API Keys).

Set it as an environment variable:
```
export VERBATIM_API_KEY=vb_your_key_here
```

## Platform basics

Verbatim is organized into **collections** — curated document collections. Every action endpoint accepts an optional `collection_ids` parameter; omit it for ACL Anthology, or pass `?collection_ids=other&collection_ids=anthology` for cross-collection queries when other collections are available.

## Endpoints

Base URL: `https://verbatim.krlabs.eu`

All requests require the header: `Authorization: Bearer $VERBATIM_API_KEY`

---

### Verbatim Transform (collection-agnostic)

Turn any question + context documents into a verbatim answer with citations. Not limited to ACL papers — works with any text. Use this when you have your own documents/context and want a cited answer.

```bash
curl -s -X POST "https://verbatim.krlabs.eu/api/v1/transform/verbatim" \
  -H "Authorization: Bearer $VERBATIM_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "question": "What are the main findings?",
    "context": [
      {
        "content": "The full text of document 1...",
        "title": "Document Title",
        "source": "https://example.com/doc1"
      },
      {
        "content": "The full text of document 2...",
        "title": "Another Document",
        "source": "https://example.com/doc2"
      }
    ]
  }'
```

Each context item has:
- `content` (required): the text to cite from
- `title` (optional): document title
- `source` (optional): URL or reference
- `metadata` (optional): any additional metadata

The response includes a verbatim answer where every claim is backed by a direct quote from the provided context.

---

### Ask a Research Question (RAG query)

Run RAG over the default collection (anthology) and get a verbatim answer with citations.

```bash
curl -s -X POST "https://verbatim.krlabs.eu/api/v1/query" \
  -H "Authorization: Bearer $VERBATIM_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "question": "What is the attention mechanism in transformers?",
    "hybrid_weights": {"full_text": 0.5, "dense": 0.5}
  }'
```

Scope to a specific collection (or multiple) with `collection_ids`:
```json
{
  "question": "What are recent advances in NER?",
  "collection_ids": ["anthology"],
  "filter": "metadata[\"year\"] >= 2022",
  "hybrid_weights": {"full_text": 0.5, "dense": 0.5}
}
```

Filter examples:
- `metadata["year"] == 2023` — papers from 2023
- `metadata["year"] >= 2020 and metadata["year"] <= 2024` — year range
- `metadata["booktitle"] == "Proceedings of ACL"` — specific proceedings

---

### List Collections

```bash
curl -s "https://verbatim.krlabs.eu/api/v1/collections" \
  -H "Authorization: Bearer $VERBATIM_API_KEY"
```

---

### Search Papers

Search papers by keywords with optional year filter and collection scope.

```bash
curl -s "https://verbatim.krlabs.eu/api/v1/papers/search?query=attention+mechanisms&limit=10" \
  -H "Authorization: Bearer $VERBATIM_API_KEY"
```

With year filter:
```bash
curl -s "https://verbatim.krlabs.eu/api/v1/papers/search?query=attention&year=2023&limit=10" \
  -H "Authorization: Bearer $VERBATIM_API_KEY"
```

Cross-collection (when other collections exist) — repeat the query param:
```bash
curl -s "https://verbatim.krlabs.eu/api/v1/papers/search?query=attention&collection_ids=anthology&collection_ids=biorxiv" \
  -H "Authorization: Bearer $VERBATIM_API_KEY"
```

Include the matched chunks per paper (free — no LLM, no quota cost). Useful when you want the underlying evidence the ranking was built on:
```bash
curl -s "https://verbatim.krlabs.eu/api/v1/papers/search?query=attention&include_chunks=true&limit=5" \
  -H "Authorization: Bearer $VERBATIM_API_KEY"
```
Each item in `items` then carries a `matched_chunks: [{text, score}, ...]` field listing every retrieved chunk that contributed to that paper's match.

---

### Get Paper Metadata

```bash
curl -s "https://verbatim.krlabs.eu/api/v1/papers/{paper_id}" \
  -H "Authorization: Bearer $VERBATIM_API_KEY"
```

---

### Get Paper Content (Full Text)

```bash
curl -s "https://verbatim.krlabs.eu/api/v1/papers/{paper_id}/content" \
  -H "Authorization: Bearer $VERBATIM_API_KEY"
```

---

### Get BibTeX Citation

```bash
curl -s "https://verbatim.krlabs.eu/api/v1/papers/{paper_id}/bibtex" \
  -H "Authorization: Bearer $VERBATIM_API_KEY"
```

---

### Browse Authors

Search authors by name (fuzzy, typo-tolerant).

```bash
curl -s "https://verbatim.krlabs.eu/api/v1/facets?field=author&q=vaswani&limit=20" \
  -H "Authorization: Bearer $VERBATIM_API_KEY"
```

---

### Browse Venues

```bash
curl -s "https://verbatim.krlabs.eu/api/v1/facets?field=venue&q=acl&limit=50" \
  -H "Authorization: Bearer $VERBATIM_API_KEY"
```

---

### Browse Booktitles

```bash
curl -s "https://verbatim.krlabs.eu/api/v1/facets?field=booktitle&q=emnlp&limit=50" \
  -H "Authorization: Bearer $VERBATIM_API_KEY"
```

---

### List Publication Years

```bash
curl -s "https://verbatim.krlabs.eu/api/v1/facets?field=year&limit=100" \
  -H "Authorization: Bearer $VERBATIM_API_KEY"
```

## Usage Notes

- RAG queries (`/api/v1/query`) and Verbatim Transform (`/api/v1/transform/verbatim`) count towards your monthly quota (50 free, 1000 pro)
- Search and browse endpoints don't count towards the quota
- Queries may take a few seconds — they perform retrieval + LLM generation
- Responses include verbatim citations with exact quotes from source documents
- Use `jq` to parse JSON responses, e.g. `| jq '.answer'`
- `collection_ids` defaults to `["anthology"]` everywhere it's accepted
