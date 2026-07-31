---
name: Model an object type, create records, and connect them
description: Define an object type with attributes, create records, and link them with a typed relationship in the Nex context graph.
api: openapi/nex-openapi-original.json
operations: [createObjectDefinition, createAttributeDefinition, createRecord, createRelationshipDefinition, createRelationshipInstance]
---

# Model the graph in Nex

Nex models a workspace as an Entity-Attribute-Value knowledge graph: object
definitions are the schema, records are the entities, and relationships are
typed edges. (Nex is a context/memory layer, not a CRM.)

## Auth

Every request needs an API key with the right scopes:

```
Authorization: Bearer sk-YOUR_API_KEY
```

Base URL: `https://app.nex.ai/api/developers`. This flow needs
`object.write`, `record.write`, and `relationship.write`.

## Steps

1. **Create the object definition** — `createObjectDefinition`
   (`POST /v1/objects`). Give it a slug (e.g. `vendor`). Returns the object.
2. **Add attributes** — `createAttributeDefinition`
   (`POST /v1/objects/{slug}/attributes`) for each field. Attribute `type` is one
   of: text, number, email, phone, url, date, boolean, currency, location,
   select, social_profile, domain, full_name.
3. **Create records** — `createRecord` (`POST /v1/objects/{slug}`). Attribute
   values accept either a plain string or a structured object for types like
   full_name / phone / location.
4. **Define a relationship type** — `createRelationshipDefinition`
   (`POST /v1/relationships`) between two object types. Cardinality is
   `one_to_one`, `one_to_many`, or `many_to_many`.
5. **Link two records** — `createRelationshipInstance`
   (`POST /v1/records/{record_id}/relationships`).

## Rules

- Use `upsertRecord` (`PUT /v1/objects/{slug}`) instead of `createRecord` when
  you want create-or-update semantics keyed on identity.
- Errors return `{ "code": <http status>, "message": "..." }`. Handle 400
  (bad input), 401 (bad key), 429 (rate limit — back off).
- There is no idempotency-key header; prefer PUT upserts for safe retries.
