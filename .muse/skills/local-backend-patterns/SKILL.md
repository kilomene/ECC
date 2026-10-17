# Backend Patterns

Layered architecture for his backends: Cloudflare Workers with Hono, plus a FastAPI appendix for his Python services. Use when building or reviewing backend code.

## Separate HTTP, service, and repository layers

- Keep route handlers thin: parse input, call the service, return the response. Put all business logic in the service layer, and hide every data-access detail behind a repository interface.

```ts
// Hono route: HTTP layer
app.post("/api/v1/markets", async (c) => {
  const input = marketCreateSchema.parse(await c.req.json())
  const market = await marketService.create(input)
  return c.json({ data: market }, 201)
})

// Service layer: business logic only
class MarketService {
  constructor(private repo: MarketRepository) {}
  async create(input: CreateMarketInput) {
    // validate business rules here, then delegate persistence
    return this.repo.create(input)
  }
}

// Repository layer: D1 access only
class D1MarketRepository implements MarketRepository {
  constructor(private db: D1Database) {}
  async create(input: CreateMarketInput) {
    return this.db
      .prepare("INSERT INTO markets (id, title, status) VALUES (?, ?, ?)")
      .bind(input.id, input.title, input.status)
      .run()
  }
}
```

- Depend on repository interfaces in services, never on concrete storage. Swap implementations without touching business logic.

- Reach bindings through the request context in Workers. Read the D1 binding from `c.env.DB` and pass it into repositories at the route level.

## Build the middleware pipeline

- Order middleware as auth, then logging, then rate limiting, then the handler. Reject unauthenticated requests before spending any other work.

```ts
// Hono middleware pipeline
app.use("*", requestLogger)
app.use("/api/*", requireAuth)
app.use("/api/*", rateLimit({ windowMs: 60000, limit: 100 }))
```

- Validate tokens in one auth middleware and attach the user to the context. Check ownership or roles at the handler or service level, never in the repository.

- Log structured JSON with a request id, method, path, and user id. Log full error context server-side and return generic messages to clients.

- Rate limit against a shared store, not per-process memory. Workers lose in-memory state between isolates, so local counters reset silently.

## Prevent N+1 queries and index deliberately

- Never query inside a loop over query results. Fetch the related rows in one batched query and join them in memory with a lookup map.

```ts
// BAD: one query per market
for (const market of markets) {
  market.creator = await userRepo.findById(market.creator_id)
}

// GOOD: two queries total
const creators = await userRepo.findByIds(markets.map((m) => m.creator_id))
const byId = new Map(creators.map((c) => [c.id, c]))
markets.forEach((m) => { m.creator = byId.get(m.creator_id) })
```

- Select only the columns you need. Avoid `SELECT *` on tables with wide or growing columns.

- Add indexes for the filters, sorts, and joins your hot paths use. Keep each index narrow and verify it against the query plan on production-sized data.

- Wrap multi-write operations in a transaction so partial writes never persist. Keep transactions short and make no network calls inside them.

## Cache and process background jobs

- Apply cache-aside for read-heavy data: check the cache, fall back to D1, then populate the cache with a TTL. Invalidate the entry on writes.

```ts
async function getMarket(id: string, db: D1Database, kv: KVNamespace) {
  const cached = await kv.get(`market:${id}`, "json")
  if (cached) return cached
  const market = await marketRepo.findById(id)
  if (market) {
    await kv.put(`market:${id}`, JSON.stringify(market), { expirationTtl: 300 })
  }
  return market
}
```

- Give cache keys a namespace prefix and a version segment when the shape changes. Set explicit TTLs instead of caching forever.

- Never block an HTTP response on slow work. Hand background work to `c.executionCtx.waitUntil()` for short tasks or a Workers Queue for durable jobs.

```ts
app.post("/api/v1/markets", async (c) => {
  const market = await marketService.create(await c.req.json())
  c.executionCtx.waitUntil(indexMarket(market.id))
  return c.json({ data: market }, 201)
})
```

- Make queue consumers idempotent. Jobs can redeliver, so re-running one must not corrupt state.

## Appendix: FastAPI service layout

For his Python services (bots, loops, sidecars).

### Lay out the project

- Organize code into routers, models, schemas, and services. Keep `main.py` to the app factory, lifespan, and middleware.

```
app/
  main.py          # app factory, lifespan, middleware
  config.py        # pydantic-settings
  dependencies.py  # shared dependencies
  routers/         # HTTP endpoints
  models/          # SQLAlchemy ORM models
  schemas/         # Pydantic request/response schemas
  services/        # business logic
```

### Build the app factory and settings

- Build the app in a `create_app()` factory with a lifespan context manager. Close pooled resources such as the DB engine on shutdown.

- Load config through pydantic-settings with typed fields. Validate required settings at startup and fail fast when they are missing.

- Keep `Base.metadata.create_all` out of any deployed lifespan. Use it for local dev only and manage production schema with Alembic migrations.

### Split schemas and inject dependencies

- Split Pydantic v2 schemas by direction: separate request schemas from response schemas. Never return a request schema that could leak secrets like password hashes.

- Inject DB sessions and the current user with FastAPI dependencies. Compose them with `Annotated` type aliases such as `DbDep` and `CurrentUserDep`.

- Keep route handlers thin: parse with a request schema, call a service method, return a response schema. Map service errors to HTTP status codes at the route boundary.

### Write transactional services and tests

- Put business logic in service classes that take the DB session in the constructor. Commit inside the service method and roll back on integrity errors.

- Rely on atomic DB constraints instead of race-prone application-level prechecks. Catch `IntegrityError`, roll back, and raise a domain error.

- Test with pytest and httpx: override the DB dependency, run the app through `ASGITransport`, and assert real HTTP status codes. Enforce deterministic ordering on every paginated list query.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.
- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
