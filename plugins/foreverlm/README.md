# ForeverLM

Connect your AI assistant to your private ForeverLM library.

This plugin adds the official remote ForeverLM MCP server, plus four skills (the
learning workflow, a one-time Zotero or Paperpile library migration, a chat
export that brings a conversation held elsewhere into ForeverLM as a chat, and an
AI cost review that proposes cheaper model settings from your usage ledger), so an
assistant can:

- Retrieve and search your sources (articles, books, papers, podcasts, videos)
- Read and write the highlights and notes you made while reading
- Inspect recurring meeting groups and set their sidebar image
- Work with Projects and their context
- Plan and manage Learning Journeys and daily study schedules
- Run interactive review sessions and save the transcripts
- Read Slack channels you have connected, live, and file a PDF posted there as its paper
- Move a Zotero or Paperpile library into ForeverLM once, through the paper pipeline
- Bring a chat held with another assistant into ForeverLM as a chat, with its Docs and Source links intact
- Review what you spend on AI, by task and model, and change the model settings you approve

## Remote MCP server

```
https://plugin.foreverlm.com/mcp
```

No local server process is required. The plugin uses a hosted Streamable HTTP MCP
endpoint with OAuth 2.1 (PKCE + Dynamic Client Registration) and Sign in with Apple.

## Installation

### Claude Code and Cowork

```bash
claude plugin marketplace add AurifyTech/foreverlm-plugins
claude plugin install foreverlm@foreverlm
```

Run `/mcp` in Claude Code, choose **foreverlm**, and complete Sign in with Apple.

### Grok Build

```bash
grok plugin marketplace add AurifyTech/foreverlm-plugins
grok plugin install foreverlm --trust
```

Reload plugins (`r` in the Plugins tab) or start a new session. The `foreverlm`
MCP server appears under MCP servers once trusted.

### Manual install from a local checkout

```bash
claude plugin marketplace add /path/to/foreverlm-plugins
grok plugin install /path/to/foreverlm-plugins/plugins/foreverlm --trust
```

## Authentication

On first use, the assistant opens the ForeverLM authorization page. Complete Sign in
with Apple. Tokens are managed by the assistant.

An authorized always-on MCP host (iCloud relay) can serve most public tools even when
the Mac app is closed. Capabilities that require the native Mac app still need the
desktop app running and the relay active.

Always call `get_mcp_status` first in a workflow. If the plugin is unavailable,
surface the error instead of guessing library contents.

## Example usage

- "What should I study today?"
- "Plan this week's learning journey and move the Bayesian source to Friday."
- "Quiz me on today's sources using a review and save the transcript."
- "Summarize the open context for my Statistics project."
- "What have I been highlighting this week?"

## Public tool surface

The plugin exposes a focused 39-tool surface for the library, highlights, meeting
groups, Projects, Schedule, reviews, source filing, Slack reading, and scheduled
tasks. Every tool carries a title and read-only, destructive, and open-world
annotations. The gateway enforces the allowlist; calls outside it are refused.

See the full directory and behavior in the ForeverLM documentation.

## Links

- Website: https://foreverlm.com
- Support: https://foreverlm.com/support
- Privacy: https://foreverlm.com/privacy
- Terms: https://foreverlm.com/terms
- MCP setup docs: https://docs.foreverlm.com/setup-mcp

## Status

One package, versioned 1.0.2, serves every provider:

- **Claude Code and Cowork:** installable from this marketplace today; submission to
  Anthropic's plugin directory is in progress.
- **Claude (claude.ai, Desktop, mobile):** the remote MCP server is being submitted to
  the Claude Connectors Directory; until then use the custom plugin setup.
- **ChatGPT and Codex:** version 1.0.2 is in OpenAI's review.
- **Grok Build:** installable from this marketplace today.

For the full cross-provider submission record see
`docs/official-assistant-extension-submissions.md` in the ForeverLM repository.
