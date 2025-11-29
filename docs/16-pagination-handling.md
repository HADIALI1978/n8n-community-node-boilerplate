# 16 - Pagination Handling

> Implementing paginated API responses with cursor and offset patterns

---

## 🤖 AI Agent Context

**READ THIS DOCUMENT** to implement pagination for "Return All" functionality.

| Pagination Type | When to Use | API Example |
|----------------|-------------|-------------|
| Cursor-based | Modern APIs, large datasets | Notion, Slack |
| Link Header | GitHub style | GitHub API |
| Offset-based | Legacy APIs | Traditional REST |

**Key Pattern**: Always add small delays (`100ms`) between requests to respect rate limits.

**Related**:
- [12-declarative-routing.md](./12-declarative-routing.md) - Declarative pagination
- [13-custom-execute-methods.md](./13-custom-execute-methods.md) - Programmatic pagination

---

## Declarative Pagination

**Valid pagination types**:
- `'offset'` - Traditional offset/limit pagination
- `'generic'` - Flexible type for cursor, Link header, and custom patterns

> **Note**: There is no `'cursor'` type. Cursor-based pagination uses `type: 'generic'` with custom `continue` and `request` expressions.

### Link Header Pagination (GitHub style)

```typescript
// shared/utils.ts
export function parseLinkHeader(header: string | undefined): Record<string, string> {
  if (!header) return {};
  const links: Record<string, string> = {};
  const parts = header.split(',');
  for (const part of parts) {
    const match = part.match(/<([^>]+)>;\s*rel="([^"]+)"/);
    if (match) links[match[2]] = match[1];
  }
  return links;
}

// In property definition
{
  displayName: 'Return All',
  name: 'returnAll',
  type: 'boolean',
  default: false,
  routing: {
    send: {
      paginate: '={{ $value }}',
      type: 'query',
      property: 'per_page',
      value: '100',
    },
    operations: {
      pagination: {
        type: 'generic',
        properties: {
          continue: `={{ !!(${parseLinkHeader.toString()})($response.headers?.link).next }}`,
          request: {
            url: `={{ (${parseLinkHeader.toString()})($response.headers?.link)?.next ?? $request.url }}`,
          },
        },
      },
    },
  },
}
```

### Offset Pagination

```typescript
routing: {
  send: { paginate: '={{ $value }}' },
  operations: {
    pagination: {
      type: 'offset',
      properties: {
        limitParameter: 'limit',
        offsetParameter: 'offset',
        pageSize: 100,
        type: 'query',
      },
    },
  },
}
```

### Cursor Pagination

```typescript
routing: {
  send: { paginate: '={{ $value }}' },
  operations: {
    pagination: {
      type: 'generic',
      properties: {
        continue: '={{ $response.body.has_more }}',
        request: {
          qs: { cursor: '={{ $response.body.next_cursor }}' },
        },
      },
    },
  },
}
```

---

## Programmatic Pagination

### Cursor-Based (Modern APIs)

```typescript
async execute(this: IExecuteFunctions): Promise<INodeExecutionData[][]> {
  const credentials = await this.getCredentials('myApi');
  const returnAll = this.getNodeParameter('returnAll', 0) as boolean;
  const limit = this.getNodeParameter('limit', 0, 50) as number;
  
  let allResults: any[] = [];
  let cursor: string | undefined;
  
  do {
    const response = await this.helpers.httpRequest({
      method: 'GET',
      url: 'https://api.example.com/items',
      headers: { 'Authorization': `Bearer ${credentials.apiKey}` },
      qs: { cursor, limit: 100 },
    });
    
    allResults.push(...response.items);
    cursor = response.next_cursor;
    
    if (!returnAll && allResults.length >= limit) {
      allResults = allResults.slice(0, limit);
      break;
    }

    // Rate limiting: small delay between requests
    if (cursor) {
      await new Promise(resolve => setTimeout(resolve, 100));
    }
  } while (cursor);
  
  return [allResults.map(item => ({ json: item }))];
}
```

### Offset-Based (Legacy APIs)

```typescript
async execute(this: IExecuteFunctions): Promise<INodeExecutionData[][]> {
  const credentials = await this.getCredentials('myApi');
  const returnAll = this.getNodeParameter('returnAll', 0) as boolean;
  const limit = this.getNodeParameter('limit', 0, 50) as number;
  
  const allResults: any[] = [];
  let offset = 0;
  const pageSize = 100;
  
  while (true) {
    const response = await this.helpers.httpRequest({
      method: 'GET',
      url: 'https://api.example.com/items',
      headers: { 'Authorization': `Bearer ${credentials.apiKey}` },
      qs: { offset, limit: pageSize },
    });
    
    allResults.push(...response.items);
    
    // Check if we have all items
    if (response.items.length < pageSize) {
      break;
    }
    
    if (!returnAll && allResults.length >= limit) {
      break;
    }
    
    offset += pageSize;

    // Rate limiting
    await new Promise(resolve => setTimeout(resolve, 100));
  }
  
  const finalResults = returnAll ? allResults : allResults.slice(0, limit);
  return [finalResults.map(item => ({ json: item }))];
}
```

---

## Best Practices

| Practice | Why |
|----------|-----|
| Add delays between requests | Respect API rate limits |
| Use `limit` parameter | Don't fetch more than needed |
| Stream large datasets | Avoid memory issues |
| Handle partial failures | Return successful items with errors |

---

## Next Steps

- [12 - Declarative Routing](./12-declarative-routing.md)
- [13 - Custom Execute Methods](./13-custom-execute-methods.md)
