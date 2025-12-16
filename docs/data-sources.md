## Basic Concepts

**Data Sources** are API endpoints that provide data to your micro-app. Each source exposes a named dataset that can be reused across sections and components.

### Key Features:
- Fetch data from REST APIs
- Support multiple authentication methods
- Dynamic URL construction with tokens
- Automatic pagination support
- Request/response customization

---

## Data Source Structure

### Minimal Data Source

```json
{
  "dataSources": [
    "myData": {
      "url": "https://api.example.com/data",
      "method": "GET"
    }
  ]
}
```

### Full Data Source Schema

```json
{
  "dataSources": [
    "dataSourceId": {
      "url": "https://api.example.com/endpoint",
      "method": "GET",
      "once": true,
      "contentType": "application/json",
      "body": {},
      "options": {},
      "auth": {
        "strategy": "bearer_header",
        "credentials": {
          "token": "apiToken"
        }
      },
      "pagination": {
        "type": "server",
        "pageParam": "page",
        "limitParam": "limit",
        "pageSize": 20
      }
    }
  ]
}
```

### Required Keys
- `id` – unique identifier for the data source
- `url` – absolute or relative endpoint path
- `method` – HTTP method: `GET`, `POST`, `PUT`, `PATCH`

### Optional Flags
- `once: true` – fetch once even if multiple components depend on the source
- `contentType` – request content type (default: `"application/json"`)
- `body` – request body for POST/PUT/PATCH requests
- `options` – custom headers or other request options
- `auth` – authentication configuration
- `pagination` – pagination settings for large datasets

---

## Authentication

Configure how your app authenticates API requests using the `auth` block.

### Authentication Strategies

#### 1. No Authentication (`none`)

```json
{
  "auth": {
    "strategy": "none"
  }
}
```

Use when the API is public and requires no authentication.

---

#### 2. Query Parameters (`query_params`)

Adds authentication credentials as URL query parameters.

```json
{
  "auth": {
    "strategy": "query_params",
    "credentials": {
      "api_token": "apiToken",
      "client_id": "clientId"
    }
  }
}
```

**Result:** `https://api.example.com/data?api_token=abc123&client_id=client-001`

**Use Cases:**
- Simple API key authentication
- APIs expecting tokens in URL
- Multi-parameter authentication

---

#### 3. Bearer Token Header (`bearer_header`)

Adds an `Authorization: Bearer <token>` header.

```json
{
  "auth": {
    "strategy": "bearer_header",
    "credentials": {
      "token": "apiToken"
    }
  }
}
```

**Headers Sent:**
```
Authorization: Bearer abc123xyz
```

**Use Cases:**
- OAuth 2.0 APIs
- JWT authentication
- Modern REST APIs

---

#### 4. API Key Header (`api_key`)

Adds a custom header with your API key.

```json
{
  "auth": {
    "strategy": "api_key",
    "credentials": {
      "header_name": "X-API-Key",
      "api_key": "your-secret-key-here"
    }
  }
}
```

**Headers Sent:**
```
X-API-Key: your-secret-key-here
```

**Use Cases:**
- APIs with custom header authentication
- Service-to-service authentication
- Legacy API integrations

---

#### 5. Form Body Authentication (`body_form`)

Adds credentials to the request body (for POST requests).

```json
{
  "auth": {
    "strategy": "body_form",
    "credentials": {
      "username": "admin",
      "password": "apiPassword",
      "grant_type": "password"
    }
  }
}
```

**Request Body:**
```json
{
  "username": "admin",
  "password": "secret123",
  "grant_type": "password"
}
```

**Use Cases:**
- Login endpoints
- OAuth token endpoints
- Form-based authentication

---

## Context Tokens

Tokens are not used in this guide. Provide concrete values (IDs, emails, tenant codes, etc.) directly in URLs, query parameters, headers, and bodies.

---

## Request Configuration

### HTTP Method

Specify the request method:

```json
{
  "method": "GET"  // GET, POST, PUT, PATCH
}
```

### Content Type

Define the request content type:

```json
{
  "contentType": "application/json"  // Default
}
```

or

```json
{
  "contentType": "application/x-www-form-urlencoded"  // For form data
}
```

### Request Body

For POST/PUT/PATCH requests, define the body:

```json
{
  "method": "POST",
  "body": {
    "name": "Jane Doe",
    "email": "jane@example.com",
    "tenantId": "acme-tenant",
    "timestamp": "2025-12-15T10:00:00Z"
  }
}
```

### Custom Options

Add custom headers or other request options:

```json
{
  "options": {
    "headers": {
      "Accept-Language": "en-US",
      "Custom-Header": "value"
    },
    "timeout": 30000
  }
}
```

### Load Once vs. Dynamic Loading

**Load Once** (static data):
```json
{
  "once": true  // Loads only on initialization
}
```

**Dynamic Loading** (default):
```json
{
  "once": false  // Reloads when dependencies change
}
```

---

## Response Handling

### Accessing Data Source Values

Reference data from sources in your UI components:

```json
{
  "type": "Text",
  "props": {
    "text": "myData.userName"
  }
}
```

### Nested Data Access

```json
{
  "type": "Text",
  "props": {
    "text": "users[0].profile.name"
  }
}
```

### Using in Lists

```json
{
  "type": "list",
  "props": {
    "itemTitle": "name",
    "itemSubtitle": "email"
  },
  "data": {
    "source": "users"
  }
}
```

---

## Pagination

Enable pagination for large datasets.

### Server-Side Pagination

The server handles pagination logic:

```json
{
  "pagination": {
    "type": "server",
    "pageParam": "page",
    "limitParam": "limit",
    "pageSize": 20
  }
}
```

**How it works:**
- Initial request: `?page=1&limit=20`
- Next page: `?page=2&limit=20`
- Continues until no more data returned

### Client-Side Pagination

Load all data, paginate locally:

```json
{
  "pagination": {
    "type": "client",
    "pageSize": 15
  }
}
```

### Detailed Pagination Configuration

Long feeds can opt into built-in pagination:

```json
"sales": {
  "url": "https://api.example.com/sales",
  "method": "GET",
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

- `threshold` – when the user scrolls within this many rows of the end, request the next page
- `pageSize` – how many records the backend should return per page
- `clientPageSize` – optional override for how many records the UI buffers for smooth scrolling
- `serverSide` – `true` tells the runtime to send `pageParam`/`limitParam` on each request; `false` keeps pagination purely client-side
- `pageParam` / `limitParam` – query-string keys that the backend expects for pagination

> Keep pagination math consistent between client and server to avoid duplicate or skipped rows.

### Infinite Scroll

Combine with UI for infinite scrolling:

```json
{
  "dataSources": [
    "feed": {
      "url": "https://api.example.com/feed",
      "method": "GET",
      "pagination": {
        "type": "server",
        "pageParam": "offset",
        "limitParam": "count",
        "pageSize": 10
      }
    }
  ],
  "sections": [
    {
      "components": [
        {
          "type": "list",
          "props": {
            "itemTitle": "title",
            "itemSubtitle": "description"
          },
          "data": {
            "source": "feed"
          }
        }
      ]
    }
  ]
}
```

---

## Complete Examples

### Example 1: Simple Public API

```json
{
  "dataSources": [
    "headlines": {
      "url": "https://api.news.com/v1/headlines",
      "method": "GET",
      "once": false,
      "auth": {
        "strategy": "query_params",
        "credentials": {
          "apiKey": "your-api-key"
        }
      }
    }
  ],
  "sections": [
    {
      "components": [
        {
          "type": "list",
          "props": {
            "itemTitle": "title",
            "itemSubtitle": "description"
          },
          "data": {
            "source": "headlines"
          }
        }
      ]
    }
  ]
}
```

---

### Example 2: Authenticated User Data

```json
{
  "dataSources": [
    "userProfile": {
      "url": "https://api.example.com/profile",
      "method": "GET",
      "once": true,
      "auth": {
        "strategy": "bearer_header",
        "credentials": {
          "token": "apiToken"
        }
      }
    },
    "activities": {
      "url": "https://api.example.com/activities",
      "method": "GET",
      "auth": {
        "strategy": "bearer_header",
        "credentials": {
          "token": "apiToken"
        }
      },
      "pagination": {
        "type": "server",
        "pageParam": "page",
        "limitParam": "size",
        "pageSize": 25
      }
    }
  ],
  "sections": [
    {
      "components": [
        {
          "type": "Text",
          "props": {
            "text": "Welcome, Jane Doe!"
          }
        },
        {
          "type": "list",
          "props": {
            "itemTitle": "description"
          },
          "data": {
            "source": "activities"
          }
        }
      ]
    }
  ]
}
```

---

### Example 3: Form Submission with API

```json
{
  "dataSources": [
    "products": {
      "url": "https://api.orders.com/products",
      "method": "GET",
      "auth": {
        "strategy": "api_key",
        "credentials": {
          "header_name": "X-API-Key",
          "api_key": "prod-key-12345"
        }
      }
    }
  ],
  "sections": [
    {
      "components": [
        {
          "type": "Dropdown",
          "props": {
            "name": "productId",
            "label": "Product",
            "valueKey": "id",
            "labelKey": "name"
          },
          "data": {
            "source": "products"
          }
        },
        {
          "type": "TextField",
          "props": {
            "name": "quantity",
            "label": "Quantity",
            "keyboard": "number"
          }
        },
        {
          "type": "Button",
          "props": {
            "text": "Submit Order",
            "type": "Primary",
            "action": {
              "type": "submit",
              "url": "https://api.orders.com/orders",
              "method": "POST",
              "body": {
                "productId": "prod-123",
                "quantity": 2,
                "userId": "user-456"
              }
            }
          }
        }
      ]
    }
  ]
}
```

---

### Example 4: Multi-Source Dashboard

```json
{
  "dataSources": [
    "salesSummary": {
      "url": "https://api.example.com/sales/summary",
      "method": "GET",
      "once": true,
      "auth": {
        "strategy": "query_params",
        "credentials": {
          "client_id": "client-001",
          "token": "apiToken"
        }
      }
    },
    "recentOrders": {
      "url": "https://api.example.com/orders/recent",
      "method": "GET",
      "auth": {
        "strategy": "bearer_header",
        "credentials": {
          "token": "apiToken"
        }
      },
      "pagination": {
        "type": "server",
        "pageParam": "page",
        "limitParam": "limit",
        "pageSize": 10
      }
    },
    "inventory": {
      "url": "https://api.example.com/inventory/low-stock",
      "method": "GET",
      "auth": {
        "strategy": "bearer_header",
        "credentials": {
          "token": "apiToken"
        }
      }
    }
  ],
  "sections": [
    {
      "title": "Sales Overview",
      "components": [
        {
          "type": "SummaryCard",
          "props": {
            "items": [
              {
                "label": "Total Sales",
                "value": 120000
              },
              {
                "label": "This Month",
                "value": 18000
              }
            ]
          }
        }
      ]
    },
    {
      "title": "Recent Orders",
      "components": [
        {
          "type": "list",
          "props": {
            "itemTitle": "id",
            "itemSubtitle": "status"
          },
          "data": {
            "source": "recentOrders"
          }
        }
      ]
    },
    {
      "title": "Low Stock Items",
      "components": [
        {
          "type": "list",
          "props": {
            "itemTitle": "name",
            "itemSubtitle": "stock"
          },
          "data": {
            "source": "inventory"
          }
        }
      ]
    }
  ]
}
```

---

## Best Practices

### 1. **Use Meaningful IDs**
```json
"userProfile": {....} // Good: descriptive
```
vs
```json
"ds1": {....}  // Bad: unclear
```

### 2. **Load Static Data Once**
```json
"countries": {
  "url": "https://api.example.com/countries",
  "once": true  // Country list doesn't change often
}
```

### 3. **Provide Explicit URLs**
```json
{
  "url": "https://api.example.com/apps/acme-app/users/12345"
}
```
Keep URLs explicit and environment-appropriate without token interpolation.

### 4. **Use Appropriate Auth Strategies**
- Public APIs → `none` or `query_params` with API key
- Modern REST APIs → `bearer_header`
- Custom integrations → `api_key`
- Login flows → `body_form`

### 5. **Enable Pagination for Large Lists**
```json
{
  "pagination": {
    "type": "server",
    "pageParam": "page",
    "limitParam": "limit",
    "pageSize": 20
  }
}
```

### 6. **Naming Guidelines**

- Use verbs for live feeds (`orders`, `reservations`, `invoices`)
- Use nouns for reference data (`catalog`, `paymentModes`, `countries`)
- Keep names short and consistent so bindings stay readable

### 7. **Environment Strategy**

Avoid hardcoding environment-specific URLs. Instead, provide the correct base URL per environment during deployment or configuration time.

### 8. **Organize Related Data**
Group related data sources and reference them clearly:
```json
{
  "dataSources": [
    "masterData"{"url": "...", "auth": {....}},
    "itemsList"{"url": "...", "auth": {....}}
  ]
}
```

---

## Troubleshooting

### Common Issues

**Issue:** Data not loading
- Check URL is correct and accessible
- Verify authentication credentials
- Ensure network connectivity
- Check `once` setting (might need `false` for dynamic updates)

**Issue:** Authentication fails
- Verify auth strategy matches API requirements
- Check token/credential values are correct
- Ensure required credentials are available before the request
- Test with response preview to see error details

**Issue:** Tokens not resolving
- If your platform supports tokens, verify syntax matches that platform's rules
- Check context is initialized if tokens are enabled
- Ensure referenced data sources load before dependent ones

**Issue:** Pagination not working
- Verify API supports pagination with your parameters
- Check `pageParam` and `limitParam` match API expectations
- Ensure response returns array data

---
### Auth Strategy Quick Pick

| Your API Needs | Use This Strategy |
|----------------|-------------------|
| No auth | `none` |
| Token in URL | `query_params` |
| OAuth/JWT | `bearer_header` |
| Custom header | `api_key` |
| Credentials in body | `body_form` |
