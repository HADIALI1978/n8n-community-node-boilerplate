# 29 - Common Patterns and Recipes

> Solutions to common implementation challenges

---

## 🤖 AI Agent Context

**USE THIS DOCUMENT** for ready-to-use UI patterns. Copy and adapt to your node.

| Pattern | When to Use |
|---------|-------------|
| [Return All + Limit](#return-all--limit-pattern) | Paginated list endpoints |
| [Additional Fields](#additional-fields-pattern) | Optional parameters |
| [Filters](#filters-pattern) | Query filtering |
| [Dynamic Options](#dynamic-options-loading) | API-loaded dropdowns |
| [Dependent Dropdowns](#dependent-dropdowns) | Cascading selections |
| [Simplify Output](#simplify-output-toggle) | Clean response data |

**These patterns are commonly combined**. For example, a "Get Many" operation typically uses:
- Return All + Limit
- Filters
- (Optional) Simplify Output

**Related**:
- [06-node-properties-reference.md](./06-node-properties-reference.md) - Property types
- [28-complete-code-examples.md](./28-complete-code-examples.md) - Full examples

---

## Return All + Limit Pattern

```typescript
{
  displayName: 'Return All',
  name: 'returnAll',
  type: 'boolean',
  default: false,
},
{
  displayName: 'Limit',
  name: 'limit',
  type: 'number',
  default: 50,
  displayOptions: { show: { returnAll: [false] } },
  typeOptions: { minValue: 1, maxValue: 100 },
}
```

---

## Additional Fields Pattern

```typescript
{
  displayName: 'Additional Fields',
  name: 'additionalFields',
  type: 'collection',
  placeholder: 'Add Field',
  default: {},
  options: [
    { displayName: 'Field 1', name: 'field1', type: 'string', default: '' },
    { displayName: 'Field 2', name: 'field2', type: 'number', default: 0 },
  ],
}
```

---

## Filters Pattern

```typescript
{
  displayName: 'Filters',
  name: 'filters',
  type: 'collection',
  placeholder: 'Add Filter',
  default: {},
  options: [
    {
      displayName: 'Status',
      name: 'status',
      type: 'options',
      options: [
        { name: 'Active', value: 'active' },
        { name: 'Inactive', value: 'inactive' },
      ],
      default: '',
      routing: { send: { type: 'query', property: 'status' } },
    },
  ],
}
```

---

## Dynamic Options Loading

```typescript
{
  displayName: 'Project',
  name: 'project',
  type: 'options',
  typeOptions: { loadOptionsMethod: 'getProjects' },
  default: '',
}

// In node methods:
methods = {
  loadOptions: {
    async getProjects(this: ILoadOptionsFunctions) {
      const data = await fetchProjects();
      return data.map(p => ({ name: p.name, value: p.id }));
    },
  },
};
```

---

## Dependent Dropdowns

```typescript
{
  displayName: 'Task',
  name: 'task',
  type: 'options',
  typeOptions: {
    loadOptionsMethod: 'getTasks',
    loadOptionsDependsOn: ['project'],  // Reload when project changes
  },
  default: '',
}
```

---

## Simplify Output Toggle

```typescript
{
  displayName: 'Simplify Output',
  name: 'simplify',
  type: 'boolean',
  default: true,
  description: 'Return simplified output',
}

// In execute:
const simplify = this.getNodeParameter('simplify', i) as boolean;
const output = simplify ? { id: result.id, name: result.name } : result;
```

---

## Batching Pattern (Performance)

Process items in parallel batches for better performance:

```typescript
async batchCreate(this: IExecuteFunctions): Promise<INodeExecutionData[][]> {
  const items = this.getInputData();
  const batchSize = 50;
  const results: IDataObject[] = [];

  for (let i = 0; i < items.length; i += batchSize) {
    const batch = items.slice(i, i + batchSize);

    // Process batch in parallel
    const batchResults = await Promise.allSettled(
      batch.map(item =>
        this.helpers.httpRequest({
          method: 'POST',
          url: 'https://api.myservice.com/items',
          body: item.json
        })
      )
    );

    // Collect results (including errors)
    batchResults.forEach((result, index) => {
      if (result.status === 'fulfilled') {
        results.push(result.value.data);
      } else {
        results.push({ error: result.reason.message, itemIndex: i + index });
      }
    });

    // Small delay between batches to respect rate limits
    if (i + batchSize < items.length) {
      await new Promise(resolve => setTimeout(resolve, 100));
    }
  }

  return [results.map(r => ({ json: r }))];
}
```

---

## Cursor-Based Pagination Pattern

```typescript
async getAll(this: IExecuteFunctions): Promise<INodeExecutionData[][]> {
  const credentials = await this.getCredentials('myApi');
  const limit = (this.getNodeParameter('limit', 0) as number) || 100;
  
  let cursor: string | undefined;
  const allItems: IDataObject[] = [];

  while (true) {
    const response = await this.helpers.httpRequest({
      method: 'GET',
      url: 'https://api.myservice.com/items',
      headers: { 'Authorization': `Bearer ${credentials.apiKey}` },
      qs: {
        limit,
        cursor: cursor || undefined
      }
    });

    allItems.push(...response.data.items);

    if (!response.data.nextCursor || allItems.length >= limit) {
      break;
    }

    cursor = response.data.nextCursor;
    // Small delay to respect rate limits
    await new Promise(resolve => setTimeout(resolve, 100));
  }

  return [allItems.map(item => ({ json: item }))];
}
```

---

## Zod Validation Pattern

Type-safe parameter validation:

```typescript
import { z } from 'zod';

const TableOperationSchema = z.object({
  resource: z.literal('table'),
  operation: z.enum(['getAll', 'create', 'update', 'delete']),
  tableId: z.string().min(1),
  limit: z.number().min(1).max(1000).default(100)
});

// In execute:
try {
  const params = TableOperationSchema.parse({
    resource: this.getNodeParameter('resource', 0),
    operation: this.getNodeParameter('operation', 0),
    tableId: this.getNodeParameter('tableId', 0),
    limit: this.getNodeParameter('limit', 0)
  });
  // params is now type-safe
} catch (error) {
  if (error instanceof z.ZodError) {
    const messages = error.errors.map(e => `${e.path.join('.')}: ${e.message}`);
    throw new NodeOperationError(this.getNode(), `Invalid parameters: ${messages.join('; ')}`);
  }
  throw error;
}
```

---

## Next Steps

- [28 - Complete Code Examples](./28-complete-code-examples.md)
- [30 - Troubleshooting Guide](./30-troubleshooting-guide.md)
