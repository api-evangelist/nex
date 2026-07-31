---
name: Group records into lists and attach tasks and notes
description: Create a list for an object type, add records to it, and attach tasks, notes, and read the timeline in the Nex graph.
api: openapi/nex-openapi-original.json
operations: [createList, addListMember, getListRecords, createTask, createNote, getRecordTimeline]
---

# Organize records with lists, tasks, and notes

Lists are working sets of records scoped to one object type; tasks and notes
attach to individual records and appear on the record timeline.

## Auth

```
Authorization: Bearer sk-YOUR_API_KEY
```

Base URL: `https://app.nex.ai/api/developers`. Needs `object.write` +
`list.member.write` (lists), `task.write`, `note.write`, and `record.read`.

## Steps

1. **Create a list** — `createList` (`POST /v1/objects/{slug}/lists`) for an
   object type.
2. **Add records to it** — `addListMember` (`POST /v1/lists/{id}`), or
   `upsertListMember` (`PUT /v1/lists/{id}`) for create-or-update.
3. **Read list records** — `getListRecords` (`POST /v1/lists/{id}/records`).
   Uses offset pagination: `limit` / `offset`, with `total` / `has_more` in the
   response.
4. **Create a task** — `createTask` (`POST /v1/tasks`). Priorities: `low`,
   `medium`, `high`, `urgent`; supports due dates. Deleting archives (soft).
5. **Create a note** — `createNote` (`POST /v1/notes`). Deleting archives (soft).
6. **Read the record timeline** — `getRecordTimeline`
   (`GET /v1/records/{record_id}/timeline`). Cursor pagination:
   `next_cursor` / `has_next_page`.

## Rules

- Two pagination styles: offset (records/tasks/list records) vs cursor
  (timeline) — read the response fields to know which applies.
- Errors return `{ "code", "message" }`; handle 404 (missing record/list),
  429 (rate limit).
