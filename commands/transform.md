Turn any question + context documents into a verbatim answer with exact citations. Catalog-agnostic — works with any text you supply.

Requires `VERBATIM_API_KEY` environment variable. Get one at https://verbatim.krlabs.eu/api-keys

## Usage

Use this curl command to send a question with context documents:

```bash
curl -s -X POST "https://verbatim.krlabs.eu/api/v1/transform/verbatim" \
  -H "Authorization: Bearer $VERBATIM_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "question": "$ARGUMENTS",
    "context": [
      {
        "content": "PASTE_DOCUMENT_TEXT_HERE",
        "title": "Document Title",
        "source": "optional-url-or-reference"
      }
    ]
  }'
```

Each context item has:
- `content` (required): the text to cite from
- `title` (optional): document title
- `source` (optional): URL or reference
- `metadata` (optional): any additional metadata

The response includes a verbatim answer where every claim is backed by a direct quote from the provided context. Parse with `| jq '.answer'`.

This counts towards the monthly quota (50 free, 1000 pro).
