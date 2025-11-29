# 20 - Icons and Branding

> SVG icons with dark/light mode support

---

## 🤖 AI Agent Context

**USE THIS DOCUMENT** to add icons to your node and credentials.

| Requirement | Specification |
|-------------|--------------|
| Format | SVG (PNG deprecated) |
| ViewBox | `0 0 24 24` |
| Colors | `currentColor` for auto theme |
| Variants | `.svg` and `.dark.svg` (optional) |

**Quick Reference**:
```typescript
// Single icon
icon: 'file:mynode.svg'

// Light/dark variants
icon: { light: 'file:icon.svg', dark: 'file:icon.dark.svg' }
```

**Related**:
- [21-node-json-metadata.md](./21-node-json-metadata.md) - Catalog metadata

**Reference Directory**: `icons/`

---

## Icon Requirements

- **Format**: SVG strongly recommended. PNG support deprecated since ~2022; all official n8n nodes use SVG exclusively
- **ViewBox**: `viewBox="0 0 24 24"` standard (scales automatically in n8n UI)
- **Colors**: Use `stroke="currentColor"` and `fill="none"` for automatic theme adaptation (light/dark mode)
- **Variants**: Single SVG with `currentColor` auto-adapts to theme; explicit `.dark.svg` variant only needed for custom color control
- **Location**: Node directory (`nodes/MyNode/mynode.svg`) or shared `/icons/` folder
- **Size**: SVG is vector-based, no pixel size required. Rendered at appropriate size by n8n

> **Note**: PNG icons (60x60px) mentioned in pre-2022 documentation are deprecated. All 2024-2025 verified nodes use SVG. SVG failures show as 404s; PNG paths are not supported in modern n8n.

---

## Directory Structure

```
├── icons/                          # Shared icons
│   ├── myservice.svg               # Light mode
│   └── myservice.dark.svg          # Dark mode
└── nodes/MyNode/
    └── mynode.svg                  # Node-specific icon
```

---

## Referencing Icons

### Single Icon

```typescript
icon: 'file:mynode.svg'
```

### Light/Dark Variants

```typescript
icon: { 
  light: 'file:../../icons/github.svg', 
  dark: 'file:../../icons/github.dark.svg' 
}
```

### In Credentials

```typescript
icon: Icon = { 
  light: 'file:../icons/myservice.svg', 
  dark: 'file:../icons/myservice.dark.svg' 
}
```

---

## SVG Example

```xml
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
  <circle cx="12" cy="12" r="10"/>
  <path d="M12 6v6l4 2"/>
</svg>
```

---

## Best Practices

1. **Keep icons simple** - Clear at small sizes
2. **Use `currentColor`** - Adapts to context
3. **Test both modes** - Verify visibility
4. **Optimize SVGs** - Remove unnecessary metadata

---

## Next Steps

- [21 - Node JSON Metadata](./21-node-json-metadata.md)
