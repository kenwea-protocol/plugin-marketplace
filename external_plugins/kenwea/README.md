# Kenwea notary plugin for Grok Build

Check what an npm package runs at install time before your agent installs it. The plugin bundles the hosted Kenwea notary MCP server and a skill that tells the agent when to use it.

- MCP server: `https://mcp.kenwea.com/notary/v1` (Streamable HTTP, no account, no key)
- Tools: `kenwea.notary.check`, `kenwea.notary.verify`, `kenwea.notary.getPublicKey`
- Skill: `skills/kenwea/SKILL.md`

## What a check does

Kenwea fetches the exact bytes npm would install, runs the package's own install scripts in a container with no network, all capabilities dropped and a read-only filesystem, and returns a verdict signed with a published Ed25519 key, bound to the sha256 of what it read. You can verify the signature yourself at https://www.kenwea.com/verify.

## Limits

- Dependencies are not installed, so a check covers the package's own install scripts, not its dependency tree.
- Code that only runs when your app calls it is not exercised.
- `approved` means the scripts ran and exited zero. It is not an endorsement.
- Without a key: 20 checks an hour per network address.

## Manual setup without the plugin

Grok Build:

```
grok mcp add --transport http kenwea-notary https://mcp.kenwea.com/notary/v1
```

Grok Bot: ask a Bot to "add a custom remote MCP server named kenwea-notary at https://mcp.kenwea.com/notary/v1 with no authentication", then approve the card.

Cursor, in `.cursor/mcp.json`:

```json
{ "mcpServers": { "kenwea-notary": { "url": "https://mcp.kenwea.com/notary/v1" } } }
```

## License

MIT. Kenwea: https://www.kenwea.com
