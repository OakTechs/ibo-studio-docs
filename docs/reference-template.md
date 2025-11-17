# Reference Template & Next Steps

Use this template as a starting point for new screens. Replace ALL_CAPS placeholders with project-specific values.

```json
{
  "version": 1,
  "type": "screen",
  "title": "SCREEN_TITLE",
  "padding": 2,
  "appBar": { "centerTitle": true },
  "state": {
    "date": "",
    "customer_name": "",
    "item_id": "",
    "quantity": "",
    "rate": "",
    "discount": "0",
    "amount": "0.00",
    "netAmount": "0.00"
  },
  "dataSources": {
    "primaryFeed": {
      "type": "http",
      "method": "GET",
      "url": "https://API_BASE/primary"
    },
    "masterData": {
      "type": "http",
      "method": "GET",
      "url": "https://API_BASE/master",
      "cache": true,
      "once": true
    }
  },
  "sections": [
    { "id": "feed", "container": "none", "components": [/* list components */] },
    { "id": "form", "title": "Add Entry", "container": "card", "components": [/* form components */] },
    { "id": "extras", "title": "Extras", "components": [/* placeholder */] }
  ],
  "bottomNav": {
    "items": [
      { "label": "Feed", "icon": "feed", "sectionId": "feed" },
      { "label": "Add", "icon": "add", "sectionId": "form" },
      { "label": "More", "icon": "more", "sectionId": "extras" }
    ]
  }
}
```

## Where to Go Next

- Add schema validation (AJV, Zod) to catch mistakes before deployment.
- Build a playground that hot-reloads JSON so designers and engineers can iterate together.
- Layer in role-based access by toggling sections/components at runtime.
- Track analytics events (views, submissions) whenever users interact with the screen.
