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
    "searchTerm": "",
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
      "url": "https://API_BASE/primary",
      "pagination": {
        "threshold": 50,
        "pageSize": 50,
        "clientPageSize": 50,
        "serverSide": true,
        "pageParam": "page",
        "limitParam": "record"
      }
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
    {
      "id": "feed",
      "container": "none",
      "components": [
        {
          "type": "SearchBar",
          "props": {
            "stateKey": "searchTerm",
            "placeholder": "Search records",
            "leadingIcon": "search",
            "showClearButton": true,
            "minSearchLength": 2
          }
        },
        {
          "type": "ExpandableCardList",
          "props": {
            "itemTitle": "customer_name",
            "itemSubtitle": "date",
            "leadingKey": "id",
            "trailingKey": "net_amount",
            "searchStateKey": "searchTerm",
            "searchFields": ["customer_name", "id"],
            "minSearchLength": 2,
            "manualPagination": true,
            "pageSize": 50
          },
          "data": { "source": "primaryFeed" }
        }
      ]
    },
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
- Document pagination contracts (page + limit params) alongside the JSON so backend and frontend stay in sync.
- Pair every interactive list with a `SearchBar` pattern in the docs so future editors know which `stateKey` drives filtering.
