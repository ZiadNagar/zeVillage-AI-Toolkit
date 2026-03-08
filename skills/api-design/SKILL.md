---
name: api-design
description: "Use this skill when the user asks to design a REST API, create API endpoints, write an OpenAPI/Swagger specification, design a GraphQL schema, plan API versioning, or structure request/response payloads. Triggers for 'design an API', 'create endpoints for', 'write an OpenAPI spec', 'REST vs GraphQL', 'API versioning', 'pagination strategy', 'error response format', or 'webhook design'. Covers RESTful conventions, naming, pagination, filtering, error handling, rate limiting, and API documentation."
license: Complete terms in LICENSE.txt
---

# API Design

You are designing APIs that are consistent, predictable, and easy to consume. Follow REST conventions by default. Be opinionated about consistency — a mediocre standard applied uniformly beats a brilliant approach applied inconsistently.

## URL Structure

### Resource Naming

```
# Collections (plural nouns)
GET    /api/v1/users
POST   /api/v1/users

# Individual resources
GET    /api/v1/users/{id}
PUT    /api/v1/users/{id}
PATCH  /api/v1/users/{id}
DELETE /api/v1/users/{id}

# Nested resources (max 2 levels deep)
GET    /api/v1/users/{userId}/orders
GET    /api/v1/users/{userId}/orders/{orderId}

# Actions that don't map to CRUD (use verbs as exception)
POST   /api/v1/users/{id}/activate
POST   /api/v1/orders/{id}/refund
```

### Rules

- Use **plural nouns** for collections: `/users`, not `/user`
- Use **kebab-case** for multi-word resources: `/order-items`, not `/orderItems`
- Max **2 levels** of nesting — beyond that, use query parameters or top-level resources
- Use path parameters for identity (`/users/123`), query parameters for filtering (`/users?role=admin`)
- API prefix: `/api/v1/` — always version from day one

## HTTP Methods

| Method | Purpose | Idempotent | Request Body | Success Code |
| ------ | ------- | ---------- | ------------ | ------------ |
| GET | Read resource(s) | Yes | No | 200 |
| POST | Create resource | No | Yes | 201 |
| PUT | Full replace | Yes | Yes | 200 |
| PATCH | Partial update | No* | Yes | 200 |
| DELETE | Remove resource | Yes | No | 204 |

- **POST** returns `201 Created` with the created resource and a `Location` header
- **DELETE** returns `204 No Content` with no body
- **PUT** replaces the entire resource — omitted fields are set to defaults
- **PATCH** updates only provided fields — omitted fields are unchanged

## Request & Response Format

### Standard Response Envelope

```json
{
  "data": { ... },
  "meta": {
    "requestId": "req_abc123"
  }
}
```

### Collection Response (Paginated)

```json
{
  "data": [
    { "id": "usr_1", "name": "Alice", "email": "alice@example.com" },
    { "id": "usr_2", "name": "Bob", "email": "bob@example.com" }
  ],
  "meta": {
    "page": 1,
    "pageSize": 20,
    "totalCount": 142,
    "totalPages": 8
  },
  "links": {
    "self": "/api/v1/users?page=1&pageSize=20",
    "next": "/api/v1/users?page=2&pageSize=20",
    "prev": null,
    "first": "/api/v1/users?page=1&pageSize=20",
    "last": "/api/v1/users?page=8&pageSize=20"
  }
}
```

### Rules for Payloads

- Use **camelCase** for JSON field names
- Use **ISO 8601** for dates: `"2025-01-15T09:30:00Z"`
- Use **string IDs** with type prefixes: `"usr_abc123"`, `"ord_def456"`
- Null fields: include with `null` value (don't omit) for consistency
- Timestamps: always include `createdAt` and `updatedAt` on resources

## Pagination

Default strategy: **offset-based** for simple UIs, **cursor-based** for feeds/infinite scroll.

### Offset-Based

```
GET /api/v1/users?page=2&pageSize=20
```

- Default `pageSize`: 20
- Max `pageSize`: 100
- Always return `totalCount` and `totalPages`

### Cursor-Based

```
GET /api/v1/feed?cursor=eyJpZCI6MTIzfQ&limit=20
```

- Cursor is an opaque string (base64-encoded identifier)
- Return `nextCursor` (null when no more results)
- Better for real-time data, large datasets, or when items are frequently inserted

## Filtering, Sorting, Searching

```
# Filtering (field=value)
GET /api/v1/users?role=admin&status=active

# Multiple values (comma-separated)
GET /api/v1/users?role=admin,editor

# Sorting (prefix with - for descending)
GET /api/v1/users?sort=name        # ascending
GET /api/v1/users?sort=-createdAt  # descending
GET /api/v1/users?sort=-createdAt,name  # multi-sort

# Searching (full-text)
GET /api/v1/users?search=alice

# Date ranges
GET /api/v1/orders?createdAfter=2025-01-01&createdBefore=2025-02-01

# Field selection (sparse fieldsets)
GET /api/v1/users?fields=id,name,email
```

## Error Handling

### Standard Error Response

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "The request body contains invalid fields.",
    "details": [
      {
        "field": "email",
        "message": "Must be a valid email address.",
        "value": "not-an-email"
      },
      {
        "field": "age",
        "message": "Must be a positive integer.",
        "value": -5
      }
    ]
  }
}
```

### HTTP Status Codes

| Code | When to Use |
| ---- | ----------- |
| 200 | Success (GET, PUT, PATCH) |
| 201 | Resource created (POST) |
| 204 | Success, no body (DELETE) |
| 400 | Invalid request body or parameters |
| 401 | Missing or invalid authentication |
| 403 | Authenticated but not authorized |
| 404 | Resource not found |
| 409 | Conflict (duplicate, version mismatch) |
| 422 | Valid syntax but semantically invalid |
| 429 | Rate limited |
| 500 | Server error (never expose internals) |

### Error Code Convention

Use **SCREAMING_SNAKE_CASE** strings, not numeric codes:

- `VALIDATION_ERROR` — invalid input
- `NOT_FOUND` — resource doesn't exist
- `UNAUTHORIZED` — not authenticated
- `FORBIDDEN` — not authorized
- `CONFLICT` — duplicate or version conflict
- `RATE_LIMITED` — too many requests
- `INTERNAL_ERROR` — server fault (log details server-side)

## Versioning

### URL Versioning (Recommended)

```
/api/v1/users
/api/v2/users
```

- Increment major version only for breaking changes
- Support at least N-1 version (deprecation period: 6-12 months)
- Breaking changes: removing fields, changing field types, changing behavior
- Non-breaking changes: adding fields, adding endpoints, adding optional params

### Deprecation Headers

```
Sunset: Sat, 01 Jan 2026 00:00:00 GMT
Deprecation: true
Link: </api/v2/users>; rel="successor-version"
```

## Rate Limiting

Include these headers on every response:

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 87
X-RateLimit-Reset: 1705312800
Retry-After: 30          # Only on 429 responses
```

### Default Limits

| Tier | Rate | Window |
| ---- | ---- | ------ |
| Public / unauthenticated | 60 req/min | Per IP |
| Authenticated | 1000 req/min | Per API key |
| Admin / internal | 5000 req/min | Per API key |

## Authentication

### Recommended: Bearer Token

```
Authorization: Bearer <token>
```

- Use short-lived access tokens (15-60 minutes)
- Use refresh tokens for session renewal
- API keys for server-to-server (no user context)
- Never pass tokens in query strings (they appear in logs)

## Webhooks

When designing webhook integrations:

```json
{
  "id": "evt_abc123",
  "type": "order.completed",
  "createdAt": "2025-01-15T09:30:00Z",
  "data": {
    "orderId": "ord_def456",
    "total": 99.99,
    "currency": "USD"
  }
}
```

### Rules

- Include an `id` for idempotency
- Use `type` with dot notation: `resource.action`
- Sign payloads with HMAC-SHA256
- Retry with exponential backoff (1s, 2s, 4s, 8s... max 5 retries)
- Allow consumers to configure which event types they receive

## OpenAPI Specification Template

When the user asks for an API spec, generate OpenAPI 3.1:

```yaml
openapi: 3.1.0
info:
  title: [API Name]
  version: 1.0.0
  description: [Description]
servers:
  - url: https://api.example.com/v1
paths:
  /resources:
    get:
      summary: List resources
      parameters:
        - name: page
          in: query
          schema: { type: integer, default: 1 }
        - name: pageSize
          in: query
          schema: { type: integer, default: 20, maximum: 100 }
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/ResourceList"
    post:
      summary: Create resource
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/CreateResource"
      responses:
        "201":
          description: Created
```

## Rules

- Always version APIs from day one (`/api/v1/`)
- Use plural nouns for resource names
- Return consistent envelope format across all endpoints
- Include pagination metadata for all list endpoints
- Use string IDs with type prefixes
- Error responses must include machine-readable `code` and human-readable `message`
- Never expose internal errors, database details, or stack traces
- Document every endpoint — if it's not documented, it doesn't exist

## Integration with Other Skills

- **`security-audit`** — Review API endpoints for auth/authz issues
- **`testing`** — Write API contract tests and integration tests
- **`devops`** — Configure API gateway, rate limiting infrastructure
- **`code-review`** — Review API implementations for consistency
