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
