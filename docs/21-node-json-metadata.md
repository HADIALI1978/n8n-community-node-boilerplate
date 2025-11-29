# 21 - Node JSON Metadata

> Codex and documentation metadata for n8n catalog

---

## 🤖 AI Agent Context

**USE THIS DOCUMENT** to configure the `.node.json` metadata file for n8n catalog listing.

| Field | Purpose |
|-------|---------|
| `node` | Package name reference |
| `categories` | n8n panel categorization |
| `resources` | Documentation links |

**Required for**:
- Node discoverability in n8n panel
- Category filtering
- Documentation links in UI

**Related**:
- [20-icons-and-branding.md](./20-icons-and-branding.md) - Visual assets
- [25-preparing-for-publication.md](./25-preparing-for-publication.md) - Pre-publish checklist

**Reference File**: `nodes/GithubIssues/GithubIssues.node.json`

---

## File Location

```
nodes/MyNode/MyNode.node.json
```

---

## Complete Structure

```json
{
  "node": "n8n-nodes-myservice",
  "nodeVersion": "1.0",
  "codexVersion": "1.0",
  "categories": ["Productivity", "Developer Tools"],
  "resources": {
    "credentialDocumentation": [
      {
        "url": "https://github.com/org/repo#credentials"
      }
    ],
    "primaryDocumentation": [
      {
        "url": "https://github.com/org/repo"
      }
    ]
  }
}
```

---

## Fields

| Field | Description |
|-------|-------------|
| `node` | Package name |
| `nodeVersion` | Node version |
| `codexVersion` | Always "1.0" |
| `categories` | n8n panel categories |
| `resources` | Documentation links |

---

## Categories

Official categories (exact spelling/case required, as of n8n 1.79+):
- `AI`
- `Analytics`
- `Communication`
- `Core Nodes`
- `CRM`
- `Customer Support`
- `Data & Storage`
- `Development`
- `E-Commerce`
- `Email`
- `Finance`
- `HR`
- `IT Ops`
- `Marketing`
- `Productivity`
- `Sales`
- `Social Media`
- `Transform`
- `Utilities`

> **Note**: Categories are case-sensitive. Invalid categories cause nodes to be excluded from the palette/search UI. Use 1-3 categories maximum.

---

## Next Steps

- [20 - Icons and Branding](./20-icons-and-branding.md)
- [25 - Preparing for Publication](./25-preparing-for-publication.md)
