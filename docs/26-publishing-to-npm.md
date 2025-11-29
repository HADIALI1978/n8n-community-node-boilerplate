# 26 - Publishing to npm

> Automated publishing with semantic-release and GitHub Actions

---

## 🤖 AI Agent Context

**READ THIS DOCUMENT** for publishing your n8n node to npm.

| Publishing Method | Best For | Automation |
|------------------|----------|------------|
| Manual (`npm publish`) | Quick first publish | None |
| semantic-release + CI | Production nodes | Full |

**Related**:
- [25-preparing-for-publication.md](./25-preparing-for-publication.md) - Pre-publish checklist
- [27-n8n-cloud-verification.md](./27-n8n-cloud-verification.md) - Cloud verification

---

## Prerequisites

1. npm account: https://www.npmjs.com/signup
2. Logged in: `npm login`
3. Package built: `npm run build`
4. Tests passing: `npm run test`

---

## Manual Publishing

### 1. Verify Package

```bash
# Check what will be published
npm pack --dry-run
```

### 2. Publish

```bash
# Publish to npm
npm publish
```

### 3. Verify

Visit: `https://www.npmjs.com/package/n8n-nodes-your-service`

---

## Automated Publishing with semantic-release

### 1. Setup semantic-release

```bash
pnpm add -D semantic-release @semantic-release/changelog @semantic-release/github @semantic-release/npm @semantic-release/git
```

### 2. Create .releaserc.json

```json
{
  "branches": ["main"],
  "plugins": [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    ["@semantic-release/npm", { "pkgRoot": "." }],
    ["@semantic-release/git", { "assets": ["CHANGELOG.md", "package.json"] }],
    "@semantic-release/github"
  ]
}
```

### 3. GitHub Actions Workflow

```yaml
# .github/workflows/release.yml
name: Release

on:
  push:
    branches: [main]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: pnpm/action-setup@v2
      - uses: actions/setup-node@v3
        with:
          node-version: '20'
          cache: 'pnpm'
      
      - run: pnpm ci
      - run: pnpm test --coverage
      - run: pnpm build
      
      - uses: cycjimmy/semantic-release-action@v3
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

### 4. Add NPM_TOKEN Secret

1. Go to GitHub repo → Settings → Secrets → Actions
2. Add `NPM_TOKEN` from your npm account (npmjs.com → Access Tokens)

---

## Versioning Strategy (Semantic Versioning)

| Change Type | Version | Commit Prefix |
|------------|---------|---------------|
| Bug fixes, security patches | 0.0.1 → 0.0.2 | `fix:` |
| New operations, features | 0.1.0 → 0.2.0 | `feat:` |
| Breaking changes | 1.0.0 → 2.0.0 | `BREAKING CHANGE:` |

**Commit Examples:**
```bash
git commit -m "fix: handle API rate limit errors"
git commit -m "feat: add user update operation"
git commit -m "feat!: rename resource property

BREAKING CHANGE: resource prop renamed from 'user' to 'users'"
```

---

## Manual Version Updates

```bash
# Patch: 1.0.0 → 1.0.1
npm version patch

# Minor: 1.0.0 → 1.1.0  
npm version minor

# Major: 1.0.0 → 2.0.0
npm version major

# Then publish
npm publish
```

---

## Post-Publication Maintenance

**Daily:**
- Monitor GitHub issues and PR reviews
- Test latest n8n version compatibility

**Weekly:**
- Update dependencies (`pnpm update`)
- Release patch updates if needed

**Monthly:**
- Review test coverage (maintain 90%+)
- Performance benchmarks

---

## Common Issues

| Issue | Solution |
|-------|----------|
| "Package name already exists" | Choose unique name starting with `n8n-nodes-` |
| "Must be logged in" | Run `npm login` |
| "Missing files" | Check `files` in package.json |
| "Permission denied" | Check npm access token permissions |

---

## Next Steps

- [25 - Preparing for Publication](./25-preparing-for-publication.md)
- [27 - n8n Cloud Verification](./27-n8n-cloud-verification.md)
