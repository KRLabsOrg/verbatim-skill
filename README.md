# Verbatim Plugin

A [Claude Code plugin](https://code.claude.com/docs/en/plugins) for the [Verbatim platform](https://verbatim.krlabs.eu) — specialized document collections with verbatim, citation-grounded answers. Defaults to the **ACL Anthology** collection (90,000+ NLP/CL papers).

Also includes a general-purpose **Verbatim Transform** — give it any question + context documents and get a verbatim, cited answer

## Install

First add the marketplace:

```bash
claude plugin marketplace add https://github.com/KRLabsOrg/verbatim-skill
```

Then install:

```bash
claude plugin install verbatim
```

Or test locally without installing:

```bash
claude --plugin-dir /path/to/verbatim-skill
```

## Setup

1. Go to [verbatim.krlabs.eu](https://verbatim.krlabs.eu) and sign up
2. Navigate to **API Keys** and create a new key
3. Set the environment variable:

```bash
export VERBATIM_API_KEY=vb_your_key_here
```

## Commands

| Command | Description |
|---------|-------------|
| `/verbatim:search` | Search collection papers, ask research questions, browse authors/venues |
| `/verbatim:transform` | Verbatim transform any question + context into a cited answer |

## Example Usage

**Collection search & research questions:**
- `/verbatim:search papers about transformer efficiency from 2023`
- `/verbatim:search what does the research say about attention mechanisms?`

**Verbatim Transform (collection-agnostic):**
- `/verbatim:transform what are the main findings?` (with context provided)

Or just ask Claude naturally — it will use the API endpoints from the skill.

## Collection scoping

Verbatim is organized into collections. The launch collection is the ACL Anthology; more collections may be added. Every action endpoint accepts an optional `collection_ids` parameter:

- Omit it → defaults to `["anthology"]`
- Single collection: `?collection_ids=anthology` (GET) or `{"collection_ids": ["anthology"]}` (POST)
- Multiple: `?collection_ids=anthology&collection_ids=biorxiv` or `{"collection_ids": ["anthology", "biorxiv"]}`

## Available Endpoints

| Action | Endpoint | Quota |
|--------|----------|-------|
| List collections | `GET /api/v1/collections` | Free |
| Verbatim Transform (any context) | `POST /api/v1/transform/verbatim` | Counts |
| Ask a research question (RAG) | `POST /api/v1/query` | Counts |
| Search papers | `GET /api/v1/papers/search` | Free |
| Get paper metadata | `GET /api/v1/papers/{id}` | Free |
| Get full paper content | `GET /api/v1/papers/{id}/content` | Free |
| Get BibTeX citation | `GET /api/v1/papers/{id}/bibtex` | Free |
| Browse authors/venues/years | `GET /api/v1/facets` | Free |

## Links

- [Verbatim platform](https://verbatim.krlabs.eu) — The service
- [API Documentation](https://verbatim.krlabs.eu/docs) — Full API docs
- [KR Labs](https://krlabs.eu) — Built by KR Labs
- [Claude Code Plugins](https://code.claude.com/docs/en/plugins) — Plugin documentation

## License

Apache 2.0
