# Data Sources

`dataSources` centralizes every external dependency referenced inside your screen. Each key exposes a named dataset that can be reused across sections and components.

## Anatomy of a Source

```json
"dataSources": {
  "orders": {
    "type": "http",
    "method": "GET",
    "url": "https://api.example.com/orders?limit=50"
  },
  "catalog": {
    "type": "http",
    "method": "GET",
    "url": "https://api.example.com/catalog/master",
    "cache": true,
    "once": true
  }
}
```

### Required Keys
- `type`: `http`, `graphql`, `static`, or any adapter supported by your host app.
- `method`: `GET`, `POST`, etc. Ignored for static sources.
- `url`: Absolute or relative endpoint path.

### Optional Flags
- `cache: true` – cache the response for the current session.
- `once: true` – fetch once even if multiple components depend on the source.
- `headers` / `query` / `body` – customize requests as needed.

### Pagination Block

Long feeds can opt into built-in pagination by adding a `pagination` object to any HTTP source:

```json
"sales": {
  "type": "http",
  "method": "GET",
  "url": "https://API_BASE/sales",
  "pagination": {
    "threshold": 50,
    "pageSize": 50,
    "clientPageSize": 50,
    "serverSide": true,
    "pageParam": "page",
    "limitParam": "record"
  }
}
```

- `threshold` – when the user scrolls within this many rows of the end, request the next page.
- `pageSize` – how many records the backend should return per page.
- `clientPageSize` – optional override for how many records the UI buffers for smooth scrolling.
- `serverSide` – `true` tells the runtime to send `pageParam`/`limitParam` on each request; `false` keeps pagination purely client-side.
- `pageParam` / `limitParam` – query-string keys that the backend expects for pagination.

> Keep pagination math consistent between client and server to avoid duplicate or skipped rows. When in doubt, log the outgoing query params in dev builds.

## Naming Guidelines

- Use verbs for live feeds (`orders`, `reservations`, `invoices`).
- Use nouns for reference data (`catalog`, `paymentModes`, `countries`).
- Keep names short and consistent so `@datasource.<name>` bindings stay readable.

## Environment Strategy

Avoid hardcoding environment-specific URLs. Instead:
- Inject a base URL at runtime (e.g., `@env.API_BASE`).
- Or define tokens (e.g., `{{API_BASE}}`) that your build pipeline replaces per environment.

## Testing Checklist

- Do sources return within acceptable SLA? Add client-side loading states if needed.
- Are errors surfaced to the user? Consider a default toast/snackbar on failure.
- Does cached data require manual busting? Pair `cache: true` with backend cache headers when possible.
