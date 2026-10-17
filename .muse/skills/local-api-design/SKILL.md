# API Design

REST conventions for his Cloudflare Workers APIs. Apply these when adding or reviewing any endpoint.

## Name resources consistently

- Write every resource as a plural lowercase kebab-case noun, and carry filters in query parameters instead of putting verbs in the path.

```
# GOOD
GET /api/v1/users
GET /api/v1/team-members
GET /api/v1/orders?status=active
GET /api/v1/users/123/orders

# BAD
GET /api/v1/getUsers
GET /api/v1/team_members
GET /api/v1/user
GET /api/v1/users/123/getOrders
```

- Nest sub-resources one level deep to express ownership. Keep nesting shallow since long chains break easily.

- Reserve POST with a verb suffix for the rare action that does not map to CRUD. Keep the resource noun in the path.

```
POST /api/v1/orders/abc-123/cancel
POST /api/v1/auth/refresh
```

## Use HTTP semantics correctly

- Use GET for reads, POST for creates and actions, PUT for full replacement, PATCH for partial updates, and DELETE for removal.

- Return 201 Created with a Location header on every successful create, pointing at the new resource.

```ts
// Hono handler on a Cloudflare Worker
app.post("/api/v1/users", async (c) => {
  const user = await userService.create(await c.req.json())
  c.header("Location", `/api/v1/users/${user.id}`)
  return c.json({ data: user }, 201)
})
```

- Return 400 for malformed requests and 422 for valid JSON that fails validation. Include field-level details in the error.

```
# GOOD
HTTP/1.1 422 Unprocessable Entity
{ "error": { "code": "validation_error", "message": "Request validation failed",
  "details": [{ "field": "email", "message": "Must be a valid email address" }] } }

# BAD
HTTP/1.1 500 Internal Server Error    never use for a validation error
HTTP/1.1 200 OK                       never use for a failed create
```

- Never return 500 for a client error. Reserve 500 for unexpected server failures and return a generic message.

- Return 204 with an empty body for deletes and bodyless updates. Return 200 only when the response carries data.

## Standardize request and response shapes

- Wrap every success payload in one envelope across all endpoints. Return single resources as `{ "data": ... }`.

- Wrap every error payload in one error envelope. Give each error a machine-readable code, a human message, and optional field details.

```json
{
  "error": {
    "code": "not_found",
    "message": "User not found"
  }
}
```

- Never signal failure inside a 200 response. Set the real HTTP status code and let the envelope carry the truth.

- Use one field-naming convention across every endpoint. Match the Flutter app's expectations on the Nova backend boundary.

## Paginate, filter, and sort predictably

- Paginate every list endpoint. Use offset pagination for admin views and small datasets under 10K rows.

```
GET /api/v1/users?page=2&per_page=20
```

- Use cursor pagination for feeds, infinite scroll, and large datasets. Return `has_next` and `next_cursor` in the meta object.

```
GET /api/v1/users?cursor=abc123&limit=20
```

- Filter with plain query parameters for equality and bracket notation for comparisons.

```
GET /api/v1/orders?status=active&customer_id=abc-123
GET /api/v1/products?price[gte]=10&price[lte]=100
GET /api/v1/products?category=electronics,clothing
```

- Sort with one `sort` parameter, prefixing `-` for descending order and comma-separating multiple fields.

```
GET /api/v1/products?sort=-created_at,price
```

- Add a `q` parameter for full-text search and a `fields` parameter for sparse fieldsets when payloads grow.

## Version the API and run the checklist

- Version with a URL prefix such as `/api/v1/`. Run at most two active versions at once.

- Ship additive changes (new fields, new optional parameters, new endpoints) in the current version. Reserve a new version for removed fields, renamed fields, changed types, or changed URL structure.

- Announce deprecations with a Sunset header well before removal. Return 410 Gone after the sunset date.

- Confirm the URL is plural, kebab-case, and verb-free.

- Confirm the method and status codes match their semantics.

- Confirm input is validated against a schema.

- Confirm errors follow the standard envelope.

- Confirm list endpoints paginate.

- Confirm auth is required or the endpoint is explicitly public.

- Confirm ownership checks keep users inside their own resources.

- Confirm responses leak no stack traces or internal details.

- Confirm field naming matches the existing convention.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.
- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
