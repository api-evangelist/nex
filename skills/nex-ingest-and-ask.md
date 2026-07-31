---
name: Ingest context and ask grounded questions
description: Push unstructured context into the Nex graph, then ask natural-language questions grounded in it and search records.
api: openapi/nex-openapi-original.json
operations: [addContext, queryContext, getArtifact, searchRecords]
---

# Ingest context and ask grounded questions

Nex parses unstructured text into its knowledge graph so agents can reason over
the same grounded context Nex itself uses.

## Auth

```
Authorization: Bearer sk-YOUR_API_KEY
```

Base URL: `https://app.nex.ai/api/developers`. Needs `record.write` (ingest)
and `record.read` (ask / search).

## Steps

1. **Add context** — `addContext` (`POST /v1/context/text`). Submit raw text,
   transcripts, or artifacts. Ingestion is asynchronous.
2. **Poll the artifact** — `getArtifact`
   (`GET /v1/context/artifacts/{artifact_id}`) until processing is complete.
   A `410 Gone` means the artifact expired.
3. **Ask a question** — `queryContext` (`POST /v1/context/ask`). Get a grounded
   natural-language answer backed by the graph.
4. **Search records** — `searchRecords` (`POST /v1/search`) for full-text search
   across records; results are grouped by object type with relevance scores.

## Rules

- Ingestion and AI jobs are async: expect `202 Accepted`, then poll.
- Errors use `{ "code", "message" }`; 429 = rate limited, back off and retry.
- For a real-time signal feed instead of polling, subscribe to the Insights SSE
  stream (`streamInsights`, `GET /v1/insights/stream`, scope `insight.stream`).
