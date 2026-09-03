# ForeverLM plugins

Official plugin packages for [ForeverLM](https://foreverlm.com), the learning
app that keeps your articles, papers, books, podcasts, videos, and meetings in
one private library and lets an AI assistant study them with you using the
Feynman technique.

Every package here wraps the same production remote MCP server:

```
https://plugin.foreverlm.com/mcp
```

The server speaks Streamable HTTP and authenticates with OAuth 2.1 (PKCE and
Dynamic Client Registration) backed by Sign in with Apple. Your library stays in
your own iCloud container; the plugin adds no local process.

## Claude Code and Cowork

```bash
claude plugin marketplace add AurifyTech/foreverlm-plugins
claude plugin install foreverlm@foreverlm
```

Then run `/mcp` in Claude Code, choose **foreverlm**, and complete Sign in with
Apple. The plugin is also submitted to Anthropic's plugin directory; once it is
published there, `/plugin` offers it without adding this marketplace first.

## Grok Build

```bash
grok plugin marketplace add AurifyTech/foreverlm-plugins
grok plugin install foreverlm --trust
```

## ChatGPT and Codex

ForeverLM is submitted to the OpenAI Plugins Directory, which ChatGPT and Codex
share. Until it is published there, follow the
[custom ChatGPT setup](https://docs.foreverlm.com/setup-mcp#chatgpt-custom-setup).

## Layout

| Path | Purpose |
| --- | --- |
| `.claude-plugin/marketplace.json` | Claude Code marketplace manifest |
| `.grok-plugin/marketplace.json` | Grok Build marketplace manifest |
| `plugins/foreverlm/` | The plugin: provider manifests, `.mcp.json`, the learning-workflow skill, and icons |

This repository is mirrored from the private ForeverLM monorepo by
`scripts/publish-plugins-repo.sh` there; pull requests here are not merged
directly. Questions and problems: [foreverlm.com/support](https://foreverlm.com/support).

## License

The plugin packages in this repository are released under the MIT License (see
`LICENSE`). The ForeverLM app and service are proprietary.
