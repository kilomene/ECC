# Error Handling

Typed error discipline for his TypeScript (Workers) and Python (bots, loops) code. Use when designing error types, retries, circuit breakers, or user-facing failure messages.

## Type your errors

- Build a typed error hierarchy with a code and a status code on every error. Extend one base class so handlers can branch on type.

```ts
class AppError extends Error {
  constructor(
    message: string,
    public readonly code: string,
    public readonly statusCode: number = 500,
    public readonly details?: unknown
  ) {
    super(message)
    Object.setPrototypeOf(this, new.target.prototype)
  }
}

class NotFoundError extends AppError {
  constructor(resource: string, id: string) {
    super(`${resource} not found: ${id}`, "NOT_FOUND", 404)
  }
}

class ValidationError extends AppError {
  constructor(message: string, details: { field: string, message: string }[]) {
    super(message, "VALIDATION_ERROR", 422, details)
  }
}
```

- Mirror the hierarchy in Python with a base exception carrying a code and a status code. Raise domain exceptions from services and map them at the boundary.

```python
class AppError(Exception):
    def __init__(self, message: str, code: str, status_code: int = 500):
        super().__init__(message)
        self.code = code
        self.status_code = status_code

class NotFoundError(AppError):
    def __init__(self, resource: str, id: str):
        super().__init__(f"{resource} not found: {id}", "NOT_FOUND", 404)
```

- Document every error code a client may receive. Treat errors as part of the API contract.

## Separate user messages from developer logs

- Route every thrown error through one API error handler. Map known error types to their status codes and return the standard error envelope.

```ts
// Hono error handler
app.onError((err, c) => {
  if (err instanceof AppError) {
    const body = { error: { code: err.code, message: err.message } }
    return c.json(err.details ? { error: { ...body.error, details: err.details } } : body, err.statusCode)
  }
  console.error("Unexpected error:", err)
  return c.json({ error: { code: "INTERNAL_ERROR", message: "An unexpected error occurred" } }, 500)
})
```

- Keep user-facing messages friendly and free of technical detail. Log the full context, including stack traces and request ids, server-side only.

- Never return stack traces, SQL errors, or internal paths to clients. Return a generic message with the error code for unexpected failures.

## Use Result returns for no-throw paths

- Return a Result value instead of throwing when failure is expected and common, such as parsing input or calling flaky external services.

```ts
type Result<T, E = AppError> =
  | { ok: true, value: T }
  | { ok: false, error: E }

async function fetchUser(id: string): Promise<Result<User>> {
  try {
    const user = await userRepo.findById(id)
    if (!user) return { ok: false, error: new NotFoundError("User", id) }
    return { ok: true, value: user }
  } catch (e) {
    return { ok: false, error: new AppError("Database error", "DB_ERROR") }
  }
}
```

- Check the result at the call site before touching the value. Let the type system carry the error branch.

## Retry with backoff and respect hard stops

- Retry only transient failures: network errors and 5xx responses. Never retry 4xx client errors since the request itself is wrong.

- Treat a 429 or a 403 rate-limit response as a hard stop for that provider in the current task. Stop the provider work, report the limit hit, and return control to the parent instead of retrying through it.

- Back off exponentially with jitter between attempts. Cap the delay and the attempt count.

```ts
async function withRetry<T>(fn: () => Promise<T>, maxAttempts = 3): Promise<T> {
  let attempt = 0
  while (true) {
    attempt += 1
    try {
      return await fn()
    } catch (err) {
      const clientError = err instanceof AppError && err.statusCode < 500
      if (attempt >= maxAttempts || clientError) throw err
      const delay = Math.min(500 * 2 ** (attempt - 1) + Math.random() * 500, 10000)
      await new Promise((r) => setTimeout(r, delay))
    }
  }
}
```

- Open a circuit breaker after repeated failures to one dependency. Fail fast while the circuit is open, probe with one request after a cooldown, and close the circuit when the probe succeeds.

## Apply the five principles

- Fail fast and loudly at the boundary where the error occurs. Never bury an error where it cannot be seen.

- Keep errors typed with structure instead of plain string messages. Give every error a code.

- Never swallow an exception silently. Handle it, re-throw it, or log it in every catch block.

- Write user messages for humans and developer logs for debugging. Keep the two separate.

- Document error codes as part of the API contract. Clients depend on them.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.
- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
