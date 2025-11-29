# 23 - Testing Strategies

> Unit testing with Vitest and 90%+ coverage target

---

## 🤖 AI Agent Context

**READ THIS DOCUMENT** to implement comprehensive testing for your n8n node.

| Test Type | Purpose | Coverage Target |
|-----------|---------|-----------------|
| Unit Tests | Test services independently | 90%+ |
| Integration Tests | Test workflow execution | Key flows |
| Manual Tests | Verify in n8n UI | All operations |

**Key Tools**: Vitest is the default in `n8n-nodes-starter` (faster than Jest, native ESM support). n8n core uses Jest internally, but Vitest is recommended for community nodes.

> **Note**: n8n official docs focus on manual/UI testing. Unit tests are community best practice, not officially mandated. However, 90%+ coverage is strongly recommended for production nodes.

**Related**:
- [22-development-workflow.md](./22-development-workflow.md) - Development setup
- [30-troubleshooting-guide.md](./30-troubleshooting-guide.md) - Debug issues

---

## Vitest Configuration

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';
import path from 'path';

export default defineConfig({
  test: {
    globals: true,
    environment: 'node',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html', 'lcov'],
      include: ['src/**/*.ts', 'nodes/**/*.ts'],
      exclude: ['**/*.test.ts', '**/__tests__/**'],
      branches: 90,
      functions: 90,
      lines: 90,
      statements: 90
    },
    setupFiles: ['./test/setup.ts']
  },
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src')
    }
  }
});
```

---

## Unit Tests: Service Pattern

```typescript
// __tests__/MyApiNode.test.ts
import { describe, it, expect, beforeEach, vi } from 'vitest';
import type { IExecuteFunctions } from 'n8n-workflow';
import { TableOperations } from '../services/TableOperations';

describe('TableOperations', () => {
  let mockExecuteFunctions: Partial<IExecuteFunctions>;

  beforeEach(() => {
    mockExecuteFunctions = {
      getNodeParameter: vi.fn((param) => {
        const params = {
          resource: 'table',
          operation: 'getAll',
          tableId: 'tbl_123'
        };
        return params[param as keyof typeof params];
      }),
      getCredentials: vi.fn().mockResolvedValue({ apiKey: 'test-key' }),
      helpers: {
        httpRequest: vi.fn(),
        returnJsonArray: vi.fn((data) => [{ json: data }]),
        isNodeApiError: vi.fn(() => false)
      },
      getNode: vi.fn(() => ({ name: 'test' })),
      getInputData: vi.fn(() => [])
    };
  });

  it('fetches all table rows', async () => {
    const mockData = [{ id: 1, name: 'Row 1' }, { id: 2, name: 'Row 2' }];
    (mockExecuteFunctions.helpers!.httpRequest as any).mockResolvedValue({
      data: mockData
    });

    const operations = new TableOperations();
    const result = await operations.getAll.call(mockExecuteFunctions as IExecuteFunctions);

    expect(mockExecuteFunctions.helpers!.httpRequest).toHaveBeenCalledWith(
      expect.objectContaining({
        method: 'GET',
        url: expect.stringContaining('/tables/tbl_123/rows')
      })
    );
    expect(result).toEqual([{ json: mockData }]);
  });

  it('handles API errors gracefully', async () => {
    const error = new Error('API Error');
    (mockExecuteFunctions.helpers!.httpRequest as any).mockRejectedValue(error);

    const operations = new TableOperations();

    await expect(
      operations.getAll.call(mockExecuteFunctions as IExecuteFunctions)
    ).rejects.toThrow();
  });

  it('handles empty responses', async () => {
    (mockExecuteFunctions.helpers!.httpRequest as any).mockResolvedValue({
      data: []
    });

    const operations = new TableOperations();
    const result = await operations.getAll.call(mockExecuteFunctions as IExecuteFunctions);

    expect(result).toEqual([{ json: [] }]);
  });

  it('respects pagination limits', async () => {
    (mockExecuteFunctions.getNodeParameter as any).mockImplementation((param) => {
      const params = { limit: 50 };
      return params[param as keyof typeof params];
    });

    (mockExecuteFunctions.helpers!.httpRequest as any).mockResolvedValue({
      data: Array(50).fill(null).map((_, i) => ({ id: i }))
    });

    const operations = new TableOperations();
    const result = await operations.getAll.call(mockExecuteFunctions as IExecuteFunctions);

    expect(result[0].json).toHaveLength(50);
  });
});
```

---

## Run Tests

```bash
pnpm test                 # Run all tests
pnpm test --watch        # Watch mode
pnpm test --coverage     # Coverage report
pnpm test --debug        # Debug mode
```

---

## Manual Testing

### 1. Start Dev Server
```bash
npm run dev
```

### 2. Create Test Workflow
- Add your node to canvas
- Configure credentials
- Connect to trigger (Manual Trigger)
- Execute and verify output

### 3. Test Each Operation
- Create: Verify data is created
- Get: Verify correct data returned
- Update: Verify changes applied
- Delete: Verify removal

### 4. Test Edge Cases
- Empty inputs
- Invalid IDs
- Missing required fields
- API errors (rate limits, auth failures)

---

## Credential Testing

1. Go to **Credentials** > **New**
2. Select your credential type
3. Enter test credentials
4. Click **Test** button
5. Verify success message

---

## Testing Checklist

- [ ] All operations work correctly
- [ ] Unit tests pass with 90%+ coverage
- [ ] Error messages are helpful
- [ ] Credentials validate properly
- [ ] Pagination works (if applicable)
- [ ] Dynamic dropdowns populate
- [ ] Continue On Fail works
- [ ] Output data structure is correct

---

## Debugging Tips

1. **Console logs**: Check browser dev tools
2. **n8n logs**: Check terminal running dev server
3. **Network tab**: Inspect API requests
4. **Expression editor**: Test expressions in n8n
5. **Vitest UI**: Run `pnpm test --ui` for visual debugging

---

## Next Steps

- [22 - Development Workflow](./22-development-workflow.md)
- [30 - Troubleshooting Guide](./30-troubleshooting-guide.md)
