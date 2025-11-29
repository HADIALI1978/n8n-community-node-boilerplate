# 30 - Troubleshooting Guide

> Common issues and solutions

---

## 🤖 AI Agent Context

**READ THIS DOCUMENT** when encountering errors or unexpected behavior. Quick reference for common issues.

| Symptom | Jump To |
|---------|---------|
| Node not visible | [Node Doesn't Appear](#node-doesnt-appear) |
| Credential test fails | [Credential Test Fails](#credential-test-fails) |
| Import errors | ["Cannot find module" Errors](#cannot-find-module-errors) |
| Empty API responses | [Operations Return Empty](#operations-return-empty) |
| Dropdowns empty | [Dynamic Dropdowns Empty](#dynamic-dropdowns-empty) |
| Build errors | [Build Fails](#build-fails) |

**Related Documents**:
- [17-error-handling-patterns.md](./17-error-handling-patterns.md) - Implement error handling
- [22-development-workflow.md](./22-development-workflow.md) - Dev server issues
- [24-linting-and-code-quality.md](./24-linting-and-code-quality.md) - Lint errors

---

## Node Doesn't Appear

**Symptoms**: Node not visible in n8n panel

**Solutions**:
1. Check `package.json` paths point to `dist/`
2. Verify file names match class names
3. Restart dev server: `npm run dev`
4. Check for build errors in terminal

---

## Credential Test Fails

**Symptoms**: "Credentials not working" error

**Solutions**:
1. Verify API URL in test request
2. Check authorization header format
3. Ensure test endpoint exists
4. Verify credential name matches node reference

---

## "Cannot find module" Errors

**Symptoms**: TypeScript import errors

**Solutions**:
1. Run `npm install`
2. Check `peerDependencies` has `n8n-workflow`
3. Verify `tsconfig.json` include paths

---

## Operations Return Empty

**Symptoms**: No data returned from API calls

**Solutions**:
1. Check `requestDefaults.baseURL`
2. Verify routing URLs are correct
3. Check browser network tab for actual requests
4. Verify API response structure

---

## Dynamic Dropdowns Empty

**Symptoms**: loadOptions returns nothing

**Solutions**:
1. Check credentials are valid
2. Verify API endpoint in loadOptions
3. Add error logging to debug
4. Check dependent parameters exist

---

## Build Fails

**Symptoms**: TypeScript compilation errors

**Solutions**:
1. Run `npm run lint:fix`
2. Check TypeScript version
3. Verify all imports exist
4. Check for missing type definitions

---

## Common Error Messages

| Error | Cause | Fix |
|-------|-------|-----|
| "Property 'X' does not exist" | Missing interface property | Add required property |
| "Cannot read property of undefined" | Null reference | Add null checks |
| "Request failed with status 401" | Auth issue | Check credentials |
| "Request failed with status 404" | Wrong URL | Verify endpoint path |

---

## Common Pitfalls & Solutions

| Pitfall | Symptom | Solution |
|---------|---------|----------|
| **No error handling** | Node crashes on API error | Use `try-catch` + `NodeOperationError` |
| **Hardcoded credentials** | Security vulnerability | Always use `getCredentials()` |
| **Synchronous operations** | Node hangs n8n | Ensure all operations are `async` |
| **Large arrays in memory** | OOM on big datasets | Use pagination/streaming patterns |
| **No pagination** | API rate limits hit | Implement cursor/offset pagination |
| **Missing TypeScript types** | Runtime errors in production | Use strict TypeScript + Zod validation |
| **Tests in production** | Bloated bundle | Exclude `**/*.test.ts` from build |
| **Timeout issues** | Long-running ops fail | Set explicit timeouts, use queue mode |
| **Initialize SDK per item** | Poor performance | Initialize SDK ONCE before loop |
| **Forget `pairedItem`** | Broken item linking | Always include `pairedItem: { item: i }` |
| **Hot-reload not working** | Manual restart needed | Check Vite config + Docker volumes |
| **Credential mocking fails** | Tests broken | Mock full IExecuteFunctions object |

---

## Performance Checklist

| Aspect | Best Practice |
|--------|---------------|
| **API Requests** | Use native n8n `httpRequest` helper |
| **Pagination** | Cursor-based, configurable limit |
| **Batching** | `Promise.allSettled()` for parallel ops |
| **Rate Limiting** | Add delays between calls (`setTimeout(100)`) |
| **Timeouts** | Set explicit timeouts (`timeout: 30000`) |
| **Error Handling** | Return partial results with errors |

---

## Getting Help

- [n8n Community Forum](https://community.n8n.io/)
- [n8n Documentation](https://docs.n8n.io/integrations/creating-nodes/)
- [GitHub Issues](https://github.com/n8n-io/n8n/issues)

---

## Next Steps

- [22 - Development Workflow](./22-development-workflow.md)
- [23 - Testing Strategies](./23-testing-strategies.md)
