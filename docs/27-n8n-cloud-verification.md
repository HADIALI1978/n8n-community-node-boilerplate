# 27 - n8n Cloud Verification

> Getting verified for n8n Cloud

---

## 🤖 AI Agent Context

**READ THIS DOCUMENT** to get your node verified for n8n Cloud.

| Requirement | Description |
|-------------|-------------|
| License | MIT or Apache-2.0 |
| Dependencies | Minimal runtime deps (zero preferred) |
| Quality | Pass n8n linter, clean code, good UX |
| Security | No vulnerabilities, no secrets, no eval() |
| Documentation | Clear README with usage instructions |

**Verification Benefits**:
- Available in n8n Cloud
- Discoverable in node panel
- Verified badge
- Increased visibility

**Prerequisites**:
- [25-preparing-for-publication.md](./25-preparing-for-publication.md) - Pre-publish checklist
- [26-publishing-to-npm.md](./26-publishing-to-npm.md) - Published to npm

---

## Benefits

- Available in n8n Cloud
- Discoverable in node panel
- Verified badge
- Increased visibility

---

## Requirements

1. **Open Source License** - MIT or Apache-2.0 recommended for verification (enables n8n bundling)
2. **Minimal runtime dependencies** - Keep `dependencies` minimal; `devDependencies` allowed. Zero runtime deps preferred for security review
3. **Quality standards** - Clean code, good UX, pass n8n linter rules
4. **Security review** - No vulnerabilities (`npm audit`), no hardcoded secrets, no `eval()`/`exec()`
5. **Documentation** - Clear README with installation and usage instructions
6. **TypeScript only** - No plain JavaScript files
7. **Test coverage** - Tests with >80% coverage recommended

---

## Submission Process

> **Note**: The `creators.n8n.io` portal referenced in older documentation is no longer active.

1. Ensure all requirements are met
2. Publish your node to npm: `npm publish --access public`
3. Open a GitHub issue or email integrations@n8n.io with:
   - Repository URL
   - npm package link
   - Test workflow JSON demonstrating functionality
4. Wait for review (typically 1-4 weeks)
5. Address feedback via email/GitHub if any
6. Upon approval, node appears in n8n Cloud canvas

---

## Tips for Approval

- Remove unnecessary dependencies
- Use n8n's HTTP helpers
- Add comprehensive error handling
- Include clear documentation
- Test thoroughly

---

## Links

- [Verification Guidelines](https://docs.n8n.io/integrations/creating-nodes/build/reference/verification-guidelines/)
- [Submission Guide](https://docs.n8n.io/integrations/creating-nodes/deploy/submit-community-nodes/)

---

## Next Steps

- [25 - Preparing for Publication](./25-preparing-for-publication.md)
- [26 - Publishing to npm](./26-publishing-to-npm.md)
